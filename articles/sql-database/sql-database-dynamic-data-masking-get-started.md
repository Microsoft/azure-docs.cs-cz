---
title: Dynamické maskování dat
description: Dynamické maskování dat omezuje vystavení citlivých dat tím, že je zamaskuje k neprivilegovaným uživatelům pro SQL Database a Azure synapse
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
ms.openlocfilehash: 2759644c68d65e76de222a0ac74f1d4900caddc0
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121248"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Maskování dynamických dat pro Azure SQL Database a Azure synapse Analytics

SQL Database dynamické maskování dat omezuje vystavení citlivých dat jejich maskováním uživatelům bez oprávnění. 

Dynamické maskování dat pomáhá předcházet neoprávněnému přístupu k citlivým datům tím, že uživatelům umožňuje určit, kolik citlivých dat se může odhalit, aby to mělo minimální dopad na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.

Například zástupce služby v centru volání může identifikovat volajících několika číslicemi čísla platební karty, ale tyto datové položky by se zástupci služby neměli plně zveřejnit. Je možné definovat pravidlo maskování, které maskuje všechny kromě posledních čtyř číslic čísla platební karty v sadě výsledků dotazu. Dalším příkladem je, že se dá definovat vhodná maska dat pro ochranu osobních údajů (PII), aby se vývojář mohl dotazovat na produkční prostředí, aniž by porušil předpisy pro dodržování předpisů.

## <a name="dynamic-data-masking-basics"></a>Základy dynamického maskování dat

Zásadu dynamického maskování dat v Azure Portal nastavíte tak, že v okně konfigurace SQL Database nebo v okně nastavení vyberete operaci Maskování dynamických dat. Tato funkce se nedá nastavit pomocí portálu pro Azure synapse (použijte prosím PowerShell nebo REST API).

### <a name="dynamic-data-masking-permissions"></a>Oprávnění k maskování dynamických dat

Dynamické maskování dat lze konfigurovat pomocí rolí správce Azure SQL Database, správce serveru nebo [Správce zabezpečení systému SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) .

### <a name="dynamic-data-masking-policy"></a>Zásady dynamického maskování dat

* **Uživatelé SQL vyloučení z maskování** – sada uživatelů SQL nebo identit AAD, které ve výsledcích dotazu SQL získávají nemaskovaná data. Uživatelům s oprávněními správce se z maskování vždycky vylučují a zobrazují se původní data bez jakékoli masky.
* **Pravidla maskování** – sada pravidel definujících určená pole, která se mají maskovat, a funkci maskování, která se používá. Určená pole lze definovat pomocí názvu schématu databáze, názvu tabulky a názvu sloupce.
* **Maskování funkcí** – sada metod, které řídí expozici dat pro různé scénáře.

| Funkce maskování | Logika maskování |
| --- | --- |
| **Výchozí** |**Úplná maskování podle datových typů určených polí**<br/><br/>• Použijte XXXX nebo míň XS, pokud je velikost pole menší než 4 znaky pro řetězcové datové typy (nchar, ntext, nvarchar).<br/>• Použijte nulovou hodnotu pro číselné datové typy (bigint, bit, Decimal, int, Money, Numeric, smallint, smallmoney, tinyint, float, reálné).<br/>• Použijte 01-01-1900 pro datové typy datum/čas (datum, datetime2, DateTime, DateTimeOffset, smalldatetime, Time).<br/>• Pro variantu SQL se používá výchozí hodnota aktuálního typu.<br/>• Pro XML se používá dokument s \< maskou/>.<br/>• Použijte prázdnou hodnotu pro speciální datové typy (tabulka časového razítka, hierarchyid, GUID, binární, image, varbinary prostorového typu). |
| **Platební karta** |**Metoda maskování, která zpřístupňuje poslední čtyři číslice určených polí** a přidá konstantní řetězec jako předponu ve formě platební karty.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Metoda maskování, která zpřístupňuje první písmeno a nahradí doménu řetězcem xxx.com** pomocí předpony konstantního řetězce ve formě e-mailové adresy.<br/><br/>aXX@XXXX.com |
| **Náhodné číslo** |**Metoda maskování, která generuje náhodné číslo** podle vybraných hranic a skutečných datových typů. Pokud jsou určené hranice stejné, pak funkce maskování je konstantní číslo.<br/><br/>![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Vlastní text** |**Metoda maskování, která zpřístupňuje první a poslední znak** a přidá vlastní řetězec odsazení uprostřed. Je-li původní řetězec kratší než nezveřejněná předpona a přípona, je použit pouze řetězec odsazení. <br/>Přípona předpony [odsazení]<br/><br/>![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Doporučená pole k maskování

Modul doporučení DDM označuje některá pole z vaší databáze jako potenciálně citlivá pole, což může být pro maskování vhodné. V okně dynamické maskování dat na portálu se zobrazí doporučené sloupce pro vaši databázi. Stačí kliknout na **Přidat masku** pro jeden nebo více sloupců a pak **Uložit** , aby se pro tato pole použili maska.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Nastavení dynamického maskování dat pro vaši databázi pomocí rutin prostředí PowerShell

### <a name="data-masking-policy"></a>Zásady maskování dat

- [Get-AzSqlDatabaseDataMaskingPolicy](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](https://docs.microsoft.com/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>Pravidla maskování dat

- [Get-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Nastavení dynamického maskování dat pro vaši databázi pomocí REST API

Pomocí REST API můžete programově spravovat zásady a pravidla maskování dat. Publikovaný REST API podporuje následující operace:

### <a name="data-masking-policies"></a>Zásady maskování dat

- [Vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/datamaskingpolicies/createorupdate): vytvoří nebo aktualizuje popisek citlivosti pro zadaný sloupec.
- [Get](https://docs.microsoft.com/rest/api/sql/datamaskingpolicies/get): Získá zásadu maskování dat databáze. 

### <a name="data-masking-rules"></a>Pravidla maskování dat

- [Vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/datamaskingrules/createorupdate): vytvoří nebo aktualizuje pravidlo maskování dat databáze.
- [Seznam podle databáze](https://docs.microsoft.com/rest/api/sql/datamaskingrules/listbydatabase): načte seznam pravidel pro maskování dat databáze.
