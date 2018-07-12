---
title: Azure SQL Data Warehouse zpráva k vydání verze. dubna 2018 | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: ae3d4c3e732024baae29f75fda6f6e821af701a2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630339"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Co je nového ve službě Azure SQL Data Warehouse? Duben 2018
Azure SQL Data Warehouse neustále obdrží vylepšení. Tento článek popisuje nové funkce a změny, které byly zavedeny v dubnu 2018.

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>Možnost došlo ke zkrácení oddílu před přepínač
Zákazníci často používají k načtení dat z jedné tabulky do jiného tak, že změníte metadata tabulky až po přepnutí jako vzor oddílu `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` syntaxe. SQL Data Warehouse nepodporuje přepnutí oddílu při cílový oddíl obsahuje data. Pokud cílový oddíl již obsahuje data, zákazník třeba zkrátit cílový oddíl a pak proveďte přepínač.

SQL Data Warehouse teď podporuje tuto operaci v jediném příkazu T-SQL.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Další informace najdete v tématu [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) článku.

## <a name="improved-query-compilation-performance"></a>Vylepšený kompilace výkon dotazů
SQL Data Warehouse zavedl sadu změn ke zlepšení kompilační krok dotazu distribuovaných dotazů. Tyto změny zkrácení doby kompilace dotazu až **10 x** snižuje celkové dotazování provádění modulů runtime. Na datových skladů s velkým množstvím objektů (tabulky, funkce, zobrazení, procedur) se projeví změny.

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>Příkazy DBCC nespotřebovávají slotů souběžnosti (Změna chování)
SQL Data Warehouse podporuje podmnožinu jazyka T-SQL [příkazy DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) například [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Dříve by využívat tyto příkazy [souběžnosti slotu](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots) snížení počtu uživatelské zatížení a dotazy, které by mohly být provedeny. `DBCC` Příkazy jsou nyní spustit do místní fronty, která nespotřebovávají prostředků slot zlepšuje celkový výkon spuštění dotazů.

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>Aktualizované chybové zprávy pro nadměrné literály (Změna chování)
Dříve, bude zahrnovat SQL Data Warehouse *přibližné* počítat při dotazu obsažené příliš mnoho znaků.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

Chybová zpráva byla aktualizována pouze označující, že jste dosáhli limitu pro literál.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Další informace najdete v tématu [dotazy](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) část [limity kapacity](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits) najdete další podrobnosti na povolené maximum.

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>Odeberou SYS. Zobrazení PDW_DATABASE_MAPPINGS (Změna chování)
To `sys.pdw_database_mappings` zobrazení se nepoužívá v SQL Data Warehouse. Dříve by s výběrem tohoto zobrazení nevracela žádné výsledky. Zobrazení se odebrala. 