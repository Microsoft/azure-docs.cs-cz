---
title: Dynamické maskování dat
description: Dynamické maskování dat omezuje vystavení citlivých dat maskováním neprivilegovaným uživatelům pro SQL Database a Azure Synapse
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synpase
ms.openlocfilehash: e5b281d59245d8fbd32b18f4ac5fe577fc7ff309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192910"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Dynamické maskování dat pro Azure SQL Database a Azure Synapse Analytics

Maskování dynamických dat sql database omezuje vystavení citlivých dat maskováním neprivilegovaným uživatelům. 

Dynamické maskování dat pomáhá předcházet neoprávněnému přístupu k citlivým datům tím, že uživatelům umožňuje určit, kolik citlivých dat se může odhalit, aby to mělo minimální dopad na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.

Například zástupce služby v call centru může identifikovat volající několika číslicemi čísla kreditní karty, ale tyto datové položky by neměly být plně vystaveny servisnímu zástupci. Lze definovat pravidlo maskování, které maskuje všechny kromě posledních čtyř číslic libovolného čísla platební karty v sadě výsledků libovolného dotazu. Jako další příklad lze definovat vhodnou datovou masku, která chrání data umožňující identifikaci osobních údajů( PII), takže vývojář může dotazovat produkční prostředí pro účely řešení potíží bez porušení předpisů.

## <a name="dynamic-data-masking-basics"></a>Základy dynamického maskování dat

Zásady maskování dynamických dat nastavíte na webu Azure Portal výběrem operace maskování dynamických dat v okně konfigurace databáze SQL nebo okně nastavení. Tuto funkci nelze nastavit pomocí portálu pro Azure Synapse (použijte rozhraní Powershell nebo REST API)

### <a name="dynamic-data-masking-permissions"></a>Oprávnění k maskování dynamických dat

Dynamické maskování dat můžete nakonfigurovat pomocí rolí správce databáze Azure SQL, správce serveru nebo [správce zabezpečení SQL.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)

### <a name="dynamic-data-masking-policy"></a>Zásady maskování dynamických dat

* **Uživatelé SQL vyloučeni z maskování** - Sada uživatelů SQL nebo identit AAD, které získat nemaskovaná data ve výsledcích dotazu SQL. Uživatelé s oprávněními správce jsou vždy vyloučeni z maskování a zobrazují původní data bez masky.
* **Maskovací pravidla** - Sada pravidel, která definují určená pole, která mají být maskována, a používanou funkci maskování. Určená pole lze definovat pomocí názvu schématu databáze, názvu tabulky a názvu sloupce.
* **Maskovací funkce** – sada metod, které řídí expozici dat pro různé scénáře.

| Maskovací funkce | Maskovací logika |
| --- | --- |
| **Výchozí** |**Úplné maskování podle datových typů určených polí**<br/><br/>• Použijte XXXX nebo méně Xs, pokud je velikost pole menší než 4 znaky pro datové typy řetězců (nchar, ntext, nvarchar).<br/>• Použijte nulovou hodnotu pro číselné datové typy (bigint, bit, desetinné číslo, int, peníze, číselné, smallint, smallmoney, tinyint, float, real).<br/>• Použijte 01-01-1900 pro datové typy data a času (datum, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Pro variantu SQL se používá výchozí hodnota aktuálního typu.<br/>• Pro XML \<se používá maskovaný/> dokumentu.<br/>• Použijte prázdnou hodnotu pro speciální datové typy (tabulka časových razítek, hierarchyid, GUID, binární, obraz, varbinary prostorové typy). |
| **Platební karta** |**Maskovací metoda, která zveřejňuje poslední čtyři číslice určených polí** a přidává konstantní řetězec jako předponu ve formě platební karty.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Maskovací metoda, která zveřejňuje první písmeno a nahradí doménu XXX.com** pomocí předpony konstantního řetězce ve formě e-mailové adresy.<br/><br/>aXX@XXXX.com |
| **Náhodné číslo** |**Maskovací metoda, která generuje náhodné číslo** podle vybraných hranic a skutečných datových typů. Pokud jsou určené hranice stejné, je maskovací funkce konstantní číslo.<br/><br/>![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Vlastní text** |**Maskovací metoda, která zveřejňuje první a poslední znak** a přidá vlastní odsazení řetězec ve středu. Pokud je původní řetězec kratší než exponovaná předpona a přípona, použije se pouze řetězec odsazení. <br/>přípona předpony<br/><br/>![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Doporučená pole, která mají být maskována

Modul doporučení DDM, označí určitá pole z databáze jako potenciálně citlivá pole, což může být vhodné pro maskování. V okně Dynamické maskování dat na portálu se zobrazí doporučené sloupce pro vaši databázi. Jediné, co musíte udělat, je kliknout na **přidat masku** pro jeden nebo více sloupců a potom **uložit,** abyste pro tato pole použili masku.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Nastavení dynamického maskování dat pro databázi pomocí rutin prostředí PowerShell

Viz [Rutiny databáze Azure SQL](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Nastavení maskování dynamických dat pro databázi pomocí rozhraní REST API

Viz [Operace pro Azure SQL Database](https://docs.microsoft.com/rest/api/sql/).
