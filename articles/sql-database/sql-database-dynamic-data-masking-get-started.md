---
title: Dynamické maskování dat
description: Dynamické maskování dat omezuje vystavení citlivých dat jejich maskováním uživatelům, kteří nejsou privilegovanými oprávněními, pro SQL Database a datový sklad.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2019
ms.openlocfilehash: 149c42829762920583948958da8252a01e35ef1f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721910"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-data-warehouse"></a>Dynamické maskování dat pro Azure SQL Database a datový sklad

SQL Database dynamické maskování dat omezuje vystavení citlivých dat jejich maskováním uživatelům bez oprávnění. 

Dynamické maskování dat pomáhá předcházet neoprávněnému přístupu k citlivým datům tím, že uživatelům umožňuje určit, kolik citlivých dat se může odhalit, aby to mělo minimální dopad na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.

Například zástupce služby v centru volání může identifikovat volajících několika číslicemi čísla platební karty, ale tyto datové položky by se zástupci služby neměli plně zveřejnit. Je možné definovat pravidlo maskování, které maskuje všechny kromě posledních čtyř číslic čísla platební karty v sadě výsledků dotazu. Dalším příkladem je, že se dá definovat vhodná maska dat pro ochranu osobních údajů (PII), aby se vývojář mohl dotazovat na produkční prostředí, aniž by porušil předpisy pro dodržování předpisů.

## <a name="dynamic-data-masking-basics"></a>Základy dynamického maskování dat

Zásadu dynamického maskování dat v Azure Portal nastavíte tak, že v okně konfigurace SQL Database nebo v okně nastavení vyberete operaci Maskování dynamických dat. Tato funkce se nedá nastavit pomocí portálu pro SQL DW (použijte prosím PowerShell nebo REST API).

### <a name="dynamic-data-masking-permissions"></a>Oprávnění k maskování dynamických dat

Dynamické maskování dat lze konfigurovat pomocí rolí správce Azure SQL Database, správce serveru nebo [Správce zabezpečení systému SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) .

### <a name="dynamic-data-masking-policy"></a>Zásady dynamického maskování dat

* **Uživatelé SQL vyloučení z maskování** – sada uživatelů SQL nebo identit AAD, které ve výsledcích dotazu SQL získávají nemaskovaná data. Uživatelům s oprávněními správce se z maskování vždycky vylučují a zobrazují se původní data bez jakékoli masky.
* **Pravidla maskování** – sada pravidel definujících určená pole, která se mají maskovat, a funkci maskování, která se používá. Určená pole lze definovat pomocí názvu schématu databáze, názvu tabulky a názvu sloupce.
* **Maskování funkcí** – sada metod, které řídí expozici dat pro různé scénáře.

| Funkce maskování | Logika maskování |
| --- | --- |
| **Výchozí** |**Úplná maskování podle datových typů určených polí**<br/><br/>• Použijte XXXX nebo míň XS, pokud je velikost pole menší než 4 znaky pro řetězcové datové typy (nchar, ntext, nvarchar).<br/>• Použijte nulovou hodnotu pro číselné datové typy (bigint, bit, Decimal, int, Money, Numeric, smallint, smallmoney, tinyint, float, reálné).<br/>• Použijte 01-01-1900 pro datové typy datum/čas (datum, datetime2, DateTime, DateTimeOffset, smalldatetime, Time).<br/>• Pro variantu SQL se používá výchozí hodnota aktuálního typu.<br/>• Pro XML se používá dokument \<maskovaná/>.<br/>• Použijte prázdnou hodnotu pro speciální datové typy (tabulka časového razítka, hierarchyid, GUID, binární, image, varbinary prostorového typu). |
| **Platební karta** |**Metoda maskování, která zpřístupňuje poslední čtyři číslice určených polí** a přidá konstantní řetězec jako předponu ve formě platební karty.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Metoda maskování, která zpřístupňuje první písmeno a nahradí doménu řetězcem xxx.com** pomocí předpony konstantního řetězce ve formě e-mailové adresy.<br/><br/>aXX@XXXX.com |
| **Náhodné číslo** |**Metoda maskování, která generuje náhodné číslo** podle vybraných hranic a skutečných datových typů. Pokud jsou určené hranice stejné, pak funkce maskování je konstantní číslo.<br/><br/>Navigační podokno ![](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Vlastní text** |**Metoda maskování, která zpřístupňuje první a poslední znak** a přidá vlastní řetězec odsazení uprostřed. Je-li původní řetězec kratší než nezveřejněná předpona a přípona, je použit pouze řetězec odsazení. <br/>prefix[padding]suffix<br/><br/>Navigační podokno ![](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Doporučená pole k maskování

Modul doporučení DDM označuje některá pole z vaší databáze jako potenciálně citlivá pole, což může být pro maskování vhodné. V okně dynamické maskování dat na portálu se zobrazí doporučené sloupce pro vaši databázi. Stačí kliknout na **Přidat masku** pro jeden nebo více sloupců a pak **Uložit** , aby se pro tato pole použili maska.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Nastavení dynamického maskování dat pro vaši databázi pomocí rutin prostředí PowerShell

Viz [rutiny Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Nastavení dynamického maskování dat pro vaši databázi pomocí REST API

Viz [operace pro Azure SQL Database](https://docs.microsoft.com/rest/api/sql/).
