---
title: Azure SQL datového skladu poznámky k verzi může 2018 | Microsoft Docs
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
ms.openlocfilehash: 843621a8f6e08b2b51d4b7abd05d0ae6c3393fe1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726029"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Co je nového v Azure SQL Data Warehouse (pravděpodobně 2018)?
Azure SQL Data Warehouse obdrží vylepšení průběžně. Tento článek popisuje nové funkce a změny, které byly zavedeny v květnu 2018. 

## <a name="gen-2-instances"></a>2. generace instancí
![ALT](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Azure SQL Data Warehouse výpočetní optimalizované Gen2 vrstvy nastaví nové výkonu standardy pro datové sklady cloudu. Zákazníci nyní získat až pětkrát lepší výkon dotazů, čtyři krát další souběžnosti a pětkrát vyšší výpočetní výkon ve srovnání s aktuální generace. Nyní může posloužit 128 souběžné dotazy z jednoho clusteru, nejvyšší všechny cloudové služby skladu data.

Najdete v článku [Turbocharge analytics cloudu s Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) blog oznámení z dat Rohan Kumar, podnikové marketingový ředitel Azure.

## <a name="features"></a>Funkce

### <a name="auto-statistics"></a>Automatické statistiky
Statistiky jsou důležité k optimalizaci generování plánu dotazu v moderl náklady na základě optimalizátory jako je například modul v SQL Data Warehouse. Když všechny dotazy jsou známy předem, je určení, co statistiky objekty je potřeba vytvořit dosažitelné úloh. Ale když systému se potýkají s ad-hoc a náhodných dotazy, které je typický pro datových skladů úlohy, správci systému může čtením předpovědi co statistiky musí vytvořit vedoucí k provádění plány potenciálně zhoršené dotazů a delší dobu odezvy na dotazy. Jedním ze způsobů ke zmírnění tohoto problému je vytvoření statistiky objektů na všechny sloupce tabulky předem. Tento proces však dodává s snížení jako objekty statistiky, musí být zachováno během procesu načítání, což delší doby načítání tabulky.

SQL Data Warehouse teď podporuje automatické vytváření objektů statistiky poskytuje větší flexibilitu, produktivity a snadné použití pro správce systémů a vývojáře, a zajistit, že systém nadále nabízejí kvality provádění plány a nejlepší časy odezvy.

Pokud chcete povolit nebo zakázat automatické statistiky vytvoření v SQL Data Warehouse, spusťte následující příkaz:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Jako nejlepší postup a pokyny, doporučujeme nastavení `AUTO_CREATE_STATISTICS` možnost k `ON`.

> [!NOTE]
> Po vytvoření automatického statistiky *ve výchozím nastavení povolené* pro všechny nové datových skladů.
>  

Najdete v článku [možnostem ALTER DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) další podrobnosti najdete v článku.

### <a name="rejected-row-support"></a>Řádek podpora odmítnuta
Zákazníci se často používají [PolyBase (externí tabulky) pro načtení dat](design-elt-data-loading.md) do SQL Data Warehouse z důvodu vysoký výkon, paralelní povaha načítání dat. PolyBase je model načítání výchozí při načítání dat prostřednictvím [Azure Data Factory](http://azure.com/adf) také. 

SQL Data Warehouse přidá schopnost definovat odmítnutých řádek umístění prostřednictvím `REJECTED_ROW_LOCATION` parametr s [vytvoření externí tabulky](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) příkaz. Po spuštění [vytvořit tabulku AS vyberte funkce CTAS ()](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) z externí tabulky všechny řádky, které nebylo možné načíst, bude uložena v souboru téměř zdroj pro další šetření. 

Najdete v článku [načíst mohli bez obav s SQL Data Warehouse PolyBase odmítl řádek umístění](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) blog další podrobnosti o chování řádek byl odmítnut.

Následující příklad ukazuje nové syntaxe pro určení odmítl řádků.

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

### <a name="alter-view"></a>PŘÍKAZ ALTER ZOBRAZENÍ
[Příkaz ALTER zobrazení](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) umožňuje uživateli upravit dříve vytvořené zobrazení bez nutnosti odstranit a vytvořit zobrazení a znovu nastavit oprávnění. 

Následující příklad změní dříve vytvořené zobrazení.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

### <a name="concatws"></a>CONCAT_WS
[CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) funkce vrátí řetězec vyplývající z zřetězení dvě nebo více hodnot způsobem začátku do konce. Oddělovač zadaný v prvním argumentu ji odděluje zřetězených hodnoty. `CONCAT_WS` Funkce je užitečná pro generování výstupu Comma-Separated hodnotu (CSV).

Následující příklad ukazuje, zřetězení sadu hodnot int oddělujte čárkami.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
Příkaz vrátí následující výsledek:
```
result
---------
1,2,3
```
Následující příklad ukazuje, zřetězení sadu různé datové hodnoty typu oddělujte čárkami.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
Příkaz vrátí následující výsledek:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```
### <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
[Sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) systémové uložené procedury vrátí informace o typech dat nepodporuje v aktuálním prostředí. Používá se obvykle nástroje pro připojení prostřednictvím připojení rozhraní ODBC pro data typu šetření.

Následující příklad načte podrobnosti pro všechny typy dat podporované službou SQL Data Warehouse.

```sql
EXEC sp_datatype_info
```

## <a name="behavior-changes"></a>Změny chování
### <a name="select-into-with-order-by"></a>SELECT INTO s klauzulí ORDER BY
SQL Data Warehouse bude nyní blokovat `SELECT INTO` dotazy, které obsahují `ORDER BY` klauzule. Tato operace by dřív úspěšné podle první řazení dat v paměti a potom vkládání do cílové tabulky Změna dat tak, aby odpovídaly tvaru tabulky.

#### <a name="previous-behavior"></a>Předchozí chování
Následující příkaz by uspět s režie další zpracování.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

#### <a name="current-behavior"></a>Aktuální chování
Následující příkaz, vyvolá výjimku, chyba oznamující `ORDER BY` klauzule není podporována v `SELECT INTO` příkaz.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
Vrátí příkaz Chyba:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

### <a name="set-parseonly-on-query-status"></a>NASTAVIT PARSEONLY na Dotázat se na stav
Pomocí `SET PARSEONLY ON` syntaxe umožňuje uživateli modulem SQL Data Warehouse, zkontrolujte syntaxi každý příkaz T-SQL a vrátil chybové zprávy, aniž by kompilování nebo spuštěním příkazu. Dříve, v [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) systémové zobrazení stav těchto příkazů by zůstat v `Running` stavu. `sys.dm_pdw_exec_requests` Zobrazení nyní vrátí stav jako `Complete`.