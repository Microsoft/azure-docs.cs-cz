---
title: Zpráva k vydání verze Azure SQL Data Warehouse června 2018 | Microsoft Docs
description: Poznámky k verzi pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4348a634fd5b2b33f36d8e79f28caf659b82ccf4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "67626151"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Co je nového v Azure SQL Data Warehouse? Červen 2018
Azure SQL Data Warehouse průběžně přijímá vylepšení. Tento článek popisuje nové funkce a změny, které byly představeny v červnu 2018. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>Uživatelem definované body obnovení
SQL Data Warehouse automaticky pořizování snímků datového skladu každých 8 hodin, což garantuje dobu trvání bodu obnovení (RPO) na osm hodin. I když tyto automatizované snímky usnadňují správu datového skladu, je potřeba pořizovat snímky v kritických časech podle potřeb vaší firmy. Například pořizování snímku přímo před významnou zátěží dat nebo nasazením nových skriptů do datového skladu a povolení bodu obnovení přímo před operací. 

SQL Data Warehouse teď podporuje [uživatelsky definované body obnovení](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) prostřednictvím rutiny [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) .

```powershell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Zabezpečení na úrovni sloupců
Správa přístupu k datům a zabezpečení v datovém skladu je zásadní pro vytváření vztahů důvěryhodnosti se zákazníky a partnery. SQL Data Warehouse [teď podporuje zabezpečení na úrovni sloupců (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) , které umožňuje upravovat oprávnění k zobrazení citlivých dat tím, že omezuje přístup uživatelů k určitým sloupcům v tabulkách, aniž by bylo nutné přenavrhovat datový sklad.

Specifikace CLS umožňuje řídit přístup k sloupcům tabulky na základě kontextu spuštění uživatele nebo členství ve skupině pomocí příkazu standard [grant](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) T-SQL. Logika omezení přístupu se nachází v samotné vrstvě databáze, nikoli z dat v jiné aplikaci, což zjednodušuje celkovou implementaci zabezpečení.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
Funkce [OBJECT_SCHEMA_NAME ()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) vrátí název schématu databáze pro objekty v oboru schématu. Tato funkce je společná v nástrojích ETL při ověřování schématu objektu. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**Ukázkové výsledky**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>Podpora zobrazení sys. time_zone_info
Zobrazení [Sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) vrací informace o podporovaných časových pásmech v rámci Azure SQL Data Warehouse.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**Ukázkové výsledky**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>Operace automatické statistiky se zobrazí v zobrazení sys. DM _pdw_exec_requests (změna chování).

Když zavedete [Automatické vytváření statistik](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic), Azure SQL Data Warehouse vygeneruje statistiku pro optimalizaci provádění dotazů. Vydání z června 2018 přidává možnost monitorování při automatickém generování statistiky přidáním záznamu do zobrazení [Sys. DM _pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) vždy, když se spustí nějaká operace [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) .

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**Ukázkové výsledky**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

## <a name="next-steps"></a>Další postup
Teď, když víte o SQL Data Warehouse, zjistíte, jak rychle [vytvořit SQL Data Warehouse][create a SQL Data Warehouse]. Pokud s Azure začínáte, můžete využít [Glosář Azure][Azure glossary], kde najdete potřebnou terminologii. Můžete se také podívat na některé z těchto dalších zdrojů ke službě SQL Data Warehouse.  

* [Úspěšné zákaznické implementace]
* [Blogy]
* [Žádosti o funkce]
* [Videa]
* [Blogy zákaznického poradního týmu]
* [Fórum Stack Overflow]
* [Twitter]


[Blogy]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogy zákaznického poradního týmu]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Úspěšné zákaznické implementace]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Žádosti o funkce]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
