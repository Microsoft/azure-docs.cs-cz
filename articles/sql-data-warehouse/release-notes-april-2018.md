---
title: Azure SQL Data Warehouse zpráva k vydání verze. dubna 2018 | Dokumentace Microsoftu
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
ms.openlocfilehash: 0a6f3551498565f5e5343fe53d373816025d944e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472826"
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
