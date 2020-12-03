---
title: Dynamické maskování dat
description: Dynamické maskování dat omezuje vystavení citlivých dat jejich maskováním uživatelům, kteří nejsou privilegovanými oprávněními, pro Azure SQL Database, Azure SQL Managed instance a Azure synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/04/2020
tags: azure-synpase
ms.openlocfilehash: f8d352dac98f953f7f6d8033d0d9e1376c4da313
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532240"
---
# <a name="dynamic-data-masking"></a>Dynamické maskování dat 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure SQL Managed instance a Azure synapse Analytics podporují dynamické maskování dat. Dynamické maskování dat minimalizuje odhalení citlivých dat tím, že je pro uživatele bez oprávnění maskuje. 

Dynamické maskování dat pomáhá předcházet neoprávněnému přístupu k citlivým datům tím, že uživatelům umožňuje určit, kolik citlivých dat se může odhalit, aby to mělo minimální dopad na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.

Například zástupce služby v centru volání může identifikovat volajících několika číslicemi čísla platební karty, ale tyto datové položky by se zástupci služby neměli plně zveřejnit. Je možné definovat pravidlo maskování, které maskuje všechny kromě posledních čtyř číslic čísla platební karty v sadě výsledků dotazu. Dalším příkladem je, že je možné definovat vhodnou masku dat pro ochranu osobních údajů, aby se vývojář mohl dotazovat na produkční prostředí, aniž by narušila předpisy pro dodržování předpisů.

## <a name="dynamic-data-masking-basics"></a>Základy dynamického maskování dat

Zásadu dynamického maskování dat v Azure Portal nastavíte tak, že v podokně Konfigurace SQL Database vyberete okno **Maskování dynamických dat** v části **zabezpečení** . Tuto funkci nelze nastavit pomocí portálu pro spravovanou instanci SQL (použijte PowerShell nebo REST API). Další informace najdete v tématu [dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking).

### <a name="dynamic-data-masking-permissions"></a>Oprávnění k maskování dynamických dat

Dynamické maskování dat lze konfigurovat pomocí rolí správce Azure SQL Database, správce serveru nebo [Správce zabezpečení systému SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) .

### <a name="dynamic-data-masking-policy"></a>Zásady dynamického maskování dat

* **Uživatelé SQL vyloučení z maskování** – sada uživatelů SQL nebo identit Azure AD, které ve výsledcích dotazu SQL získávají nemaskovaná data. Uživatelům s oprávněními správce se z maskování vždycky vylučují a zobrazují se původní data bez jakékoli masky.
* **Pravidla maskování** – sada pravidel definujících určená pole, která se mají maskovat, a funkci maskování, která se používá. Určená pole lze definovat pomocí názvu schématu databáze, názvu tabulky a názvu sloupce.
* **Maskování funkcí** – sada metod, které řídí expozici dat pro různé scénáře.

| Funkce maskování | Logika maskování |
| --- | --- |
| **Výchozí** |**Úplná maskování podle datových typů určených polí**<br/><br/>• Použijte XXXX nebo míň XS, pokud je velikost pole menší než 4 znaky pro řetězcové datové typy (nchar, ntext, nvarchar).<br/>• Použijte nulovou hodnotu pro číselné datové typy (bigint, bit, Decimal, int, Money, Numeric, smallint, smallmoney, tinyint, float, reálné).<br/>• Použijte 01-01-1900 pro datové typy datum/čas (datum, datetime2, DateTime, DateTimeOffset, smalldatetime, Time).<br/>• Pro variantu SQL se používá výchozí hodnota aktuálního typu.<br/>• Pro XML se dokument \<masked/> používá.<br/>• Použijte prázdnou hodnotu pro speciální datové typy (tabulka časového razítka, hierarchyid, GUID, binární, image, varbinary prostorového typu). |
| **Platební karta** |**Metoda maskování, která zpřístupňuje poslední čtyři číslice určených polí** a přidá konstantní řetězec jako předponu ve formě platební karty.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Metoda maskování, která zpřístupňuje první písmeno a nahradí doménu řetězcem xxx.com** pomocí předpony konstantního řetězce ve formě e-mailové adresy.<br/><br/>aXX@XXXX.com |
| **Náhodné číslo** |**Metoda maskování, která generuje náhodné číslo** podle vybraných hranic a skutečných datových typů. Pokud jsou určené hranice stejné, pak funkce maskování je konstantní číslo.<br/><br/>![Snímek obrazovky, který ukazuje metodu maskování pro generování náhodného čísla.](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **Vlastní text** |**Metoda maskování, která zpřístupňuje první a poslední znak** a přidá vlastní řetězec odsazení uprostřed. Je-li původní řetězec kratší než nezveřejněná předpona a přípona, je použit pouze řetězec odsazení. <br/>Přípona předpony [odsazení]<br/><br/>![Navigační podokno](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Doporučená pole k maskování

Modul doporučení DDM označuje některá pole z vaší databáze jako potenciálně citlivá pole, což může být pro maskování vhodné. V okně dynamické maskování dat na portálu se zobrazí doporučené sloupce pro vaši databázi. Stačí kliknout na **Přidat masku** pro jeden nebo více sloupců a pak **Uložit** , aby se pro tato pole použili maska.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Nastavení dynamického maskování dat pro vaši databázi pomocí rutin prostředí PowerShell

### <a name="data-masking-policies"></a>Zásady maskování dat

- [Get-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>Pravidla maskování dat

- [Get-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>Nastavení dynamického maskování dat pro vaši databázi pomocí REST API

Pomocí REST API můžete programově spravovat zásady a pravidla maskování dat. Publikovaný REST API podporuje následující operace:

### <a name="data-masking-policies"></a>Zásady maskování dat

- [Vytvořit nebo aktualizovat](/rest/api/sql/datamaskingpolicies/createorupdate): vytvoří nebo aktualizuje zásadu maskování dat databáze.
- [Get](/rest/api/sql/datamaskingpolicies/get): Získá zásadu maskování dat databáze. 

### <a name="data-masking-rules"></a>Pravidla maskování dat

- [Vytvořit nebo aktualizovat](/rest/api/sql/datamaskingrules/createorupdate): vytvoří nebo aktualizuje pravidlo maskování dat databáze.
- [Seznam podle databáze](/rest/api/sql/datamaskingrules/listbydatabase): načte seznam pravidel pro maskování dat databáze.
