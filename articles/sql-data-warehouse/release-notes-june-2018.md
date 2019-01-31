---
title: Azure SQL Data Warehouse zpráva k vydání verze. června 2018 | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: cae02627c539e543d27ea188d521605f187ea8a0
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475342"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Co je nového ve službě Azure SQL Data Warehouse? Červen 2018
Azure SQL Data Warehouse neustále obdrží vylepšení. Tento článek popisuje nové funkce a změny, které byly zavedeny v červnu 2018. 

## <a name="user-defined-restore-points"></a>Uživatelem definované body obnovení
SQL Data Warehouse automaticky pořizuje snímky datového skladu každých 8 hodin starat plánovaného bodu obnovení osm hodin (RPO). Když to automatizované snímky usnadnění správy zatížení spuštěných váš datový sklad, není potřeba dělat jeho snímky v kritické dobu podle potřeb vašeho podniku. Třeba pořízení snímku bezprostředně před významné datové zatížení nebo nasazení nové skripty do datového skladu povolit bod obnovení přímo před provedením operace. 

SQL Data Warehouse teď podporuje [body obnovení uživatelem definované](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) prostřednictvím [New-AzureRmSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoint) rutiny.

```PowerShell
New-AzureRmSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Zabezpečení na úrovni sloupce
Správa přístupu k datům a zabezpečení v datovém skladu je důležité pro budování důvěry se zákazníky a partnery. SQL Data Warehouse [teď podporuje zabezpečení na úrovni sloupce (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) , který umožňuje nastavit oprávnění k zobrazení citlivých dat, tím, že omezíte přístup uživatelů k určité sloupce tabulky bez nutnosti přepracovat váš datový sklad.

Specifikace CLS umožňuje řídit přístup k sloupce tabulky na základě kontextu spuštění uživatele nebo jejich členství ve skupině pomocí standardních [udělení](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) příkazu T-SQL. Logika omezení přístupu se nachází na úrovni databáze, samotný spíše než z dat v jiné aplikaci, která zjednodušuje jejich celkové implementace zabezpečení.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
[OBJECT_SCHEMA_NAME()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) funkce vrací název schématu databáze pro objekty s rozsahem schématu. Tato funkce stala běžné nástroje ETL při ověřování schématu objektu. 

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

## <a name="support-for-the-systimezoneinfo-view"></a>Podpora pro zobrazení sys.time_zone_info
[Sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) zobrazení vrátí informace o podporovaných časových pásem v rámci Azure SQL Data Warehouse.

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

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>Operace statistiky automaticky zobrazí v sys.dm_pdw_exec_requests (Změna chování)

Se zavedením [automaticky Create Statistics](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistics), Azure SQL Data Warehouse bude generování statistik pro optimalizaci provádění dotazu. Verze z června 2018 přidává možnost sledovat, když se statistiky jsou automaticky generovanou přidáním záznam do [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) zobrazit pokaždé, když některý [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) provedením operace.

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
Teď, když už víte o něco o SQL Data Warehouse, zjistěte, jak rychle [vytvořit SQL Data Warehouse][create a SQL Data Warehouse]. Pokud s Azure začínáte, může vám být užitečný [Glosář Azure][Azure glossary], kde najdete potřebnou terminologii. Můžete se také podívat na některé z těchto dalších zdrojů ke službě SQL Data Warehouse.  

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
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
