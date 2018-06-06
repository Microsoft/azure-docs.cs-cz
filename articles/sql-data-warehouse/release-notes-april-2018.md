---
title: Azure SQL datového skladu poznámky k verzi duben 2018 | Microsoft Docs
description: Poznámky k verzi pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 49ffc3ddfd586964ae8a9688aeb48fffdd327b45
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738931"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Co je nového v Azure SQL Data Warehouse (duben 2018)?
Azure SQL Data Warehouse obdrží vylepšení průběžně. Tento článek popisuje nové funkce a změny, které byly zavedeny v dubnu 2018.

## <a name="features"></a>Funkce

### <a name="ability-to-truncate-a-partition-before-a-switch"></a>Schopnost zkrátit oddíl před přepínač
Zákazníci často používají jako vzor přepnutí oddílu k načtení dat z jedné tabulky do jiného změnou metadat tabulky prostřednictvím `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` syntaxe. SQL Data Warehouse nepodporuje přepnutí oddílu při cílový oddíl obsahuje data. Pokud cílový oddíl už obsahuje data, Zákazník by potřeba zkrátit cílový oddíl a potom proveďte přepínač.

SQL Data Warehouse teď podporuje tuto operaci v jediném příkazu T-SQL.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Další informace najdete v tématu [příkaz ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) článku.

### <a name="improved-query-compilation-performance"></a>Výkon kompilace vylepšené dotazů
SQL Data Warehouse zavedl sadu změn ke zlepšení kompilační krok dotazu distribuovaných dotazů. Tyto změny vylepšovat dobu kompilace dotazu až **10 x** snižuje celkové dotaz provádění moduly runtime. Tyto změny jsou více zřejmá na datových skladů s velký počet objektů (tabulky, funkce, zobrazení, procedury).

## <a name="behavior-changes"></a>Změny chování

### <a name="dbcc-commands-do-not-consume-concurrency-slots"></a>Příkazy DBCC není využívat sloty souběžnosti
SQL Data Warehouse podporuje podmnožinu T-SQL [příkazy DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) například [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Dřív by využívat tyto příkazy [souběžnosti slotu](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots) snížit počet uživatelských zatížení nebo dotazů, které by bylo možné provést. `DBCC` Příkazy se spouštějí teď v místní fronty, který není využívat prostředků slot zvýšení celkového výkonu provádění dotazu.

### <a name="updated-error-message-for-excessive-literals"></a>Aktualizované chybovou zprávu pro nadměrné literály
Dřív by mělo zahrnovat SQL Data Warehouse *přibližnou* počet při dotazu obsažené příliš mnoho literály.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

Chybová zpráva je aktualizovaná tak, aby pouze znamenat, že jste nedosáhli limitu literálu.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Další informace najdete v tématu [dotazy](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) části [limity kapacity](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits) článku Další podrobnosti o maximální limit.

### <a name="removed-the-syspdwdatabasemappings-view"></a>Odebrat SYS. PDW_DATABASE_MAPPINGS zobrazení
To `sys.pdw_database_mappings` zobrazení se nepoužívá v SQL Data Warehouse. Dříve vyberte tento zobrazení by vrátit žádné výsledky. Odebrali jsme zobrazení. 