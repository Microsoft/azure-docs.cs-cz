---
title: Azure SQL Data Warehouse zpráva k vydání verze. května 2018. | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: d26ae755b658ce39df04788b7edeee6da7e20a47
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43288088"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Co je nového ve službě Azure SQL Data Warehouse? Květen 2018 
Azure SQL Data Warehouse neustále obdrží vylepšení. Tento článek popisuje nové funkce a změny, které byly zavedeny v květnu 2018. 

## <a name="gen-2-instances"></a>Instance Gen 2
![ALT](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) úrovni Azure SQL Data Warehouse – Compute optimalizované Gen2 nastaví nové normy výkonu pro cloudové datové sklady. Zákazníci nyní získáte až pětkrát lepší výkon dotazů, čtyři vyprší další souběžnosti a pětkrát vyšší výpočetní výkon ve srovnání s aktuální generace. Nyní může posloužit 128 souběžných dotazů z jednoho clusteru, nejvyšší kteroukoli cloudovou službu skladu data.

Zobrazit [Backendových cloudové analýzy s využitím Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) oznámení na blogu od Rohanem Kumarem, Korporátní viceprezident, Azure Data.

## <a name="auto-statistics"></a>Automatické statistiky
Statistiky jsou důležité pro optimalizaci generování plánu dotazu v moderl náklady na základě nástroje jako je například modul ve službě SQL Data Warehouse. Všechny dotazy znám předem, určení, jaké statistiky objekty je potřeba vytvořit při dosažitelný úloh. Však při systému je ad-hoc a náhodné dotazy, které je typický pro úlohy v datovém skladu dat, správci systému mohou je velmi obtížné odhadnout, co statistiky je třeba vytvořit vedoucí k plánům spuštění dotazů potenciálně neoptimální a delší dobu odezvy na dotazy. Jedním ze způsobů ke zmírnění tohoto problému je předem vytvářet objekty statistiku pro všechny sloupce tabulky. Tento proces však dodává s penále jako objekty statistiky třeba udržovat během procesu načítání, což způsobí delší dobu načítání tabulky.

SQL Data Warehouse teď podporuje automatické vytváření objektů statistiky poskytuje větší flexibilitu, produktivitu a snadnost použití pro správci systému a vývojáři přitom zajistit, že systém dál nabízí plánům spuštění kvality a nejlepší doby odezvy.

Pokud chcete povolit nebo zakázat automatické statistiky vytvoření ve službě SQL Data Warehouse, spusťte následující příkaz:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Jako nejlepší praxe a pokyny, doporučujeme nastavit `AUTO_CREATE_STATISTICS` umožňuje `ON`.

> [!NOTE]
> Statistika automatického vytvoření *ve výchozím nastavení povolené* pro všechny nové datové sklady.
>  

Zobrazit [možnostem ALTER DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) , kde najdete další podrobnosti.

## <a name="rejected-row-support"></a>Zamítnuto řádek podpory
Zákazníci často používají [PolyBase (externí tabulky) pro načtení dat](design-elt-data-loading.md) do SQL Data Warehouse z důvodu vysoký výkon, paralelní povaze dat načítání. PolyBase je výchozí model načítání při načítání dat prostřednictvím [Azure Data Factory](http://azure.com/adf) také. 

SQL Data Warehouse umožňuje definovat odmítnutý řádek umístění prostřednictvím `REJECTED_ROW_LOCATION` parametr [CREATE EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) příkazu. Po provedení příkazu [vytvořit TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) z externí tabulky, se uloží všechny řádky, které nebylo možné načíst do souboru blízko zdroj pro další šetření. 

Najdete v článku [načíst bez obav s SQL Data Warehouse PolyBase odmítl řádek umístění](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) blogu podrobné informace o chování řádek byl odmítnut.

Následující příklad ukazuje Nová syntaxe pro určení odmítl řádků.

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

## <a name="alter-view"></a>PŘÍKAZ ALTER VIEW
[Příkaz ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) umožňuje uživateli upravit dříve vytvořené zobrazení bez nutnosti odstranit a vytvořit zobrazení a znovu zásadu použijte oprávnění. 

Následující příklad upravuje dříve vytvořené zobrazení.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
[CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) funkce vrátí řetězec vyplývající z zřetězení dvou nebo více hodnot způsobem začátku do konce. Rozděluje zřetězených hodnot s oddělovačem zadané v prvním argumentu. `CONCAT_WS` Funkce je užitečná pro generování výstupu Comma-Separated hodnotu (CSV).

Následující příklad ukazuje, zřetězení sadu hodnot int čárkou.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
Příkaz vrátí následující výsledek:
```
result
---------
1,2,3
```
Následující příklad ukazuje, zřetězení sadu hodnot typu různé datové čárkou.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
Příkaz vrátí následující výsledek:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
[Sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) systémové uložené procedury vrátí informace o datových typech podporovaných v aktuálním prostředí. To se běžně používá nástroje pro připojení prostřednictvím připojení rozhraní ODBC pro datový typ šetření.

Následující příklad načte podrobnosti pro všechny typy dat podporované službou SQL Data Warehouse.

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>Vyberte INTO pomocí klauzule ORDER BY Změna chování
Teď SQL Data Warehouse zablokuje `SELECT INTO` dotazů, které obsahují `ORDER BY` klauzuli. Dříve tato operace by uspěl podle prvního řazení dat v paměti a pak vkládání do cílové tabulky Změna uspořádání dat tak, aby odpovídaly obrazec tabulky.

### <a name="previous-behavior"></a>Předchozí chování
Následující příkaz by uspěl s další režii zpracování.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>Aktuální chování
Následující příkaz, vyvolá výjimku, chyba s upozorněním `ORDER BY` klauzule není podporována v `SELECT INTO` příkazu.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
Vrátí error – příkaz:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>Nastavte PARSEONLY na stav dotazu (Změna chování)
Použití `SET PARSEONLY ON` syntaxe umožňuje uživateli být modul SQL Data Warehouse, zkontrolujte syntaxi každého příkazu T-SQL a vrátí chybové zprávy bez kompilace a spuštění příkazu. Dříve, [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) systémové zobrazení stavu pro tyto příkazy by zůstala v `Running` stavu. `sys.dm_pdw_exec_requests` Zobrazení nyní vrátí stav jako `Complete`.

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