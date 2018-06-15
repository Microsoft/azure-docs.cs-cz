---
title: Návrh tabulky – Azure SQL Data Warehouse | Microsoft Docs
description: Úvod do navrhování tabulek v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d299ff0d8e719040d503852af6056d9d87738b7d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527935"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Návrh tabulky v Azure SQL Data Warehouse

Přečtěte si klíčové koncepty pro návrh tabulky v Azure SQL Data Warehouse. 

## <a name="determine-table-category"></a>Určení kategorie tabulky 

A [hvězdicového schématu](https://en.wikipedia.org/wiki/Star_schema) uspořádá data do tabulek faktů a dimenzí. Některé tabulky se používají pro integraci nebo pracovní data předtím, než se přesune do tabulky faktů nebo dimenze. Při navrhování tabulku, rozhodněte, zda patří data tabulky faktů, dimenze nebo tabulky integrace. Toto rozhodnutí informuje příslušné tabulce struktury a rozdělení. 

- **Tabulky faktů** obsahovat kvantitativní data, která jsou běžně generované v rámci transakcí systému a potom načíst do datového skladu. Například prodejní obchodní generuje prodejní transakce každý den a potom se načte data v tabulce faktů datového skladu pro analýzu.

- **Dimenze tabulky** obsahovat atribut data, která se může změnit, ale obvykle změní zřídka. Například název a adresu zákazníka jsou uložené v tabulce dimenzí a aktualizovat pouze, pokud změny profilu zákazníka. Na co nejmenší velký fakt tabulky, název a adresu zákazníka nemusíte mít každý řádek tabulky faktů. Místo toho tabulky faktů a v tabulce dimenze můžete sdílet ID zákazníka. Dotaz se můžete zapojit do dvou tabulek pro přidružení profilu zákazníka a transakce. 

- **Integrace tabulky** poskytují místo pro integraci nebo přípravu data. Můžete vytvořit tabulku integrace jako o běžnou tabulku, externí tabulku nebo dočasné tabulky. Například můžete načíst data do pracovní tabulky, provádění transformací na datech v pracovní a pak vložení dat do tabulky produkční.

## <a name="schema-and-table-names"></a>Schéma a tabulku názvy
Datový sklad v SQL Data Warehouse je typ databáze. Všechny tabulky v datovém skladu jsou obsažena ve stejné databázi.  Tabulky nemůže připojit k napříč více datových skladů. Toto chování se liší od SQL Server, který podporuje mezidatabázové spojení. 

V databázi systému SQL Server může použít fakt, dimenze, nebo integrovat tak, aby názvy schématu. Pokud provádíte migraci do databáze SQL serveru do SQL Data Warehouse, funguje nejlépe všechny tabulky faktů, dimenzí a integrace migrace na jedno schéma v SQL Data Warehouse. Například může uložit všechny tabulky v [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) ukázkový datový sklad v rámci názvem wwi jedno schéma. Následující kód vytvoří [uživatelem definované schéma](/sql/t-sql/statements/create-schema-transact-sql) názvem wwi.

```sql
CREATE SCHEMA wwi;
```

Chcete-li zobrazit organizace tabulek v SQL Data Warehouse, že můžete použít jako předpony, jež mají názvy tabulek faktů, dimenze a int. V následující tabulce jsou uvedeny některé názvy schéma a tabulku pro WideWorldImportersDW. Porovná názvy v systému SQL Server s názvy v SQL Data Warehouse. 

| Tabulka WideWorldImportersDW  | Typ tabulky | SQL Server | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| Město | Dimenze | Dimension.City | wwi.DimCity |
| Pořadí | Fakt | Fact.Order | wwi.FactOrder |


## <a name="table-persistence"></a>Trvalost tabulky 

Tabulky ukládání dat, buď trvale ve službě Azure Storage, dočasně ve službě Azure Storage nebo v úložišti dat externí do datového skladu.

### <a name="regular-table"></a>Běžnou tabulku

O běžnou tabulku ukládá data ve službě Azure Storage v rámci datového skladu. V tabulce a data uchová, bez ohledu na to, jestli je otevřít relaci.  Tento příklad vytvoří o běžnou tabulku s dva sloupce. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Dočasné tabulky
Dočasné tabulky jenom po dobu trvání relace existuje. Dočasné tabulky můžete zabránit zobrazení dočasné výsledky jiné účely a také se potřeby pro vyčištění.  Vzhledem k tomu, že dočasných tabulek také používat místní úložiště, můžete nabízí vyšší výkon pro některé operace.  Další informace najdete v tématu [dočasných tabulek](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Externí tabulka
Externí tabulka odkazuje na data umístěná v objektu blob Azure Storage nebo Azure Data Lake Store. Při použití ve spojení s příkazem CREATE TABLE AS SELECT, výběr z externí tabulky importuje data do SQL Data Warehouse. Externí tabulky, proto jsou užitečné pro načítání dat. Načítání kurzu, najdete v části [PolyBase používá k načtení dat z Azure blob storage](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Typy dat
SQL Data Warehouse podporuje nejčastěji používané datové typy. Seznam podporované datové typy, naleznete v části [datových typů v odkaz na tabulku vytvořit](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) v příkazu CREATE TABLE. Minimalizace velikost datových typů pomáhá zlepšit výkon dotazu. Pokyny k použití datových typů, najdete v části [datové typy](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Distribuované tabulky
Základní funkce služby SQL Data Warehouse je způsob, jak můžete ukládat a pracovat v tabulkách mezi 60 [distribuce](massively-parallel-processing-mpp-architecture.md#distributions).  V tabulkách se distribuují metodou kruhového dotazování, hodnoty hash nebo replikace.

### <a name="hash-distributed-tables"></a>Distribuovat algoritmu hash tabulky
Hodnota hash distribuce distribuuje řádků na základě hodnoty ve sloupci distribuční. V tabulce distribuovat algoritmu hash je navržená ke dosáhnout vysoký výkon pro dotaz propojuje na velké tabulky. Existuje několik faktorů, které ovlivňují volba distribuční sloupce. 

Další informace najdete v tématu [návrh pokyny pro distribuované tabulky](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Replikované tabulky
Replikované tabulky obsahuje úplnou kopii tabulky, které jsou k dispozici na všech výpočetních uzlech. Vzhledem k tomu, že spojení v replikovaných tabulkách nevyžadují přesun dat, dotazy spustit rychlé na replikovaných tabulek. Replikace vyžaduje dodatečné úložiště, ale a není praktické pro rozsáhlé tabulky. 

Další informace najdete v tématu [návrh pokyny pro replikované tabulky](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Kruhové dotazování tabulky
Řádky tabulky pomocí kruhového dotazování tabulky rovnoměrně distribuuje mezi všechny distribuce. Řádky se distribuují náhodně. Je rychlé načítání dat do tabulky pomocí kruhového dotazování.  Dotazy však může vyžadovat další přesun dat než jiné metody distribuce. 

Další informace najdete v tématu [návrh pokyny pro distribuované tabulky](sql-data-warehouse-tables-distribute.md).


### <a name="common-distribution-methods-for-tables"></a>Běžné metody distribuce pro tabulky
Kategorie Tabulka často určuje, která možnost zvolit pro distribuci v tabulce. 

| Tabulky kategorií | Možnosti doporučené distribuce |
|:---------------|:--------------------|
| Fakt           | Hodnota hash distribuce pomocí clusterovaný index columnstore. Výkon zvyšuje, když jsou dvě tabulky hash spojeny ve stejném sloupci distribuce. |
| Dimenze      | Použití replikované pro menší tabulky. Pokud tabulky je příliš velký pro uložení na každém výpočetním uzlu, použijte distribuovat algoritmu hash. |
| Fázování        | Pomocí kruhového dotazování pro pracovní tabulku. Zatížení pomocí funkce CTAS je rychlé. Jakmile jsou data v přípravné tabulce, použijte příkaz INSERT... Vyberte pro přesun dat do tabulky produkční. |

## <a name="table-partitions"></a>Oddíly tabulky
Dělenou tabulku ukládá a provádí operace s řádky tabulky podle dat rozsahy. Tabulka může být například oddíly den, měsíc nebo rok. Můžete zlepšit výkon dotazů prostřednictvím odstranění oddílu, což omezí kontrolu dotaz na data v rámci oddílu. Můžete také spravovat data prostřednictvím přepnutí oddílu. Vzhledem k tomu, že data v SQL Data Warehouse je již distribuovaný, příliš mnoho oddíly můžou způsobit snížení výkonnosti dotazu. Další informace najdete v tématu [dělení pokyny](sql-data-warehouse-tables-partition.md).

## <a name="columnstore-indexes"></a>Indexy Columnstore
Ve výchozím nastavení ukládá SQL Data Warehouse tabulku jako clusterovaný index columnstore. Tato forma úložiště dat dosahuje vysoké data komprese a výkon dotazů na velké tabulky.  Clusterovaný index columnstore je většinou nejlepší volbou, ale v některých případech je clusterovaný index nebo haldy strukturu odpovídající úložiště.

Seznam funkcí columnstore najdete v tématu [co je nového pro indexy columnstore](/sql/relational-databases/indexes/columnstore-indexes-whats-new). Chcete-li zvýšit výkon index columnstore, přečtěte si téma [maximalizace rowgroup quality pro indexy columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statistika
Optimalizátor dotazů při vytváření plánu pro spuštění dotazu na používá Statistika na úrovni sloupce. Pro zlepšení výkonu dotazů, je důležité vytvořit statistiku pro jednotlivé sloupce, zejména sloupce použité v dotazu spojení. Vytváření a aktualizaci statistiky neprobíhá automaticky. [Vytvoření statistiky](/sql/t-sql/statements/create-statistics-transact-sql) po vytvoření tabulky. Aktualizujte statistiku po přidání nebo změně velký počet řádků. Například aktualizujte statistiku po zatížení. Další informace najdete v tématu [statistiky pokyny](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Příkazy pro vytváření tabulek
Můžete vytvořit tabulku jako nová prázdná tabulka. Můžete také vytvořit a naplnit tabulku s výsledky příkazu select. Toto jsou příkazy T-SQL pro vytvoření tabulky.

| Příkaz jazyka T-SQL | Popis |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Vytvoří prázdná tabulka definováním všechny sloupce tabulky a možnosti. |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) | Vytvoří externí tabulku. Definice tabulky je uložená v SQL Data Warehouse. Tabulka data se ukládají do úložiště objektů Blob v Azure nebo Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Naplní novou tabulku s výsledky příkazu select. Tabulka sloupce a datové typy jsou založené na výsledcích příkazu select. K importu dat, tento příkaz můžete vybrat z externí tabulky. |
| [VYTVOŘIT EXTERNÍ TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Vytvoří novou tabulku externí exportují se výsledky příkazu select na externí umístění.  Umístění je úložiště objektů Blob v Azure nebo Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Zarovnávání zdroj dat k datovému skladu

Ve načítání dat z jiného zdroje dat jsou naplněna tabulky datového skladu. K provedení úspěšné zatížení, musí být číslo a datové typy sloupců v zdrojová data vyrovnány s definice tabulky v datovém skladu. Získávání dat, chcete-li zarovnat může být nejtěžší součástí návrhu tabulek. 

Pokud data pocházejí z více datových úložišť, můžete přenést data do datového skladu a uložit ho v tabulce integrace. Jakmile jsou data v tabulce integrace, můžete provádět operace transformace power služby SQL Data Warehouse. Jakmile je připraven data, můžete je do provozního tabulek.

## <a name="unsupported-table-features"></a>Funkce nepodporované tabulky
SQL Data Warehouse podporuje mnoho, ale ne všechny tabulky funkce nabízené sítěmi jiné databáze.  V následujícím seznamu jsou uvedeny některé funkce tabulky, které nejsou podporovány v SQL Data Warehouse.

- Primární klíč, cizí klíče jedinečné, kontrola [omezení tabulky](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [Počítané sloupce](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Indexovaná zobrazení](/sql/relational-databases/views/create-indexed-views)
- [Pořadí](/sql/t-sql/statements/create-sequence-transact-sql)
- [Zhuštěné sloupce](/sql/relational-databases/tables/use-sparse-columns)
- [Nahrazení klíče](). Implementace s [Identity](sql-data-warehouse-tables-identity.md).
- [Synonyma.](/sql/t-sql/statements/create-synonym-transact-sql)
- [Triggery](/sql/t-sql/statements/create-trigger-transact-sql)
- [Jedinečné indexy](/sql/t-sql/statements/create-index-transact-sql)
- [Uživatelem definované typy](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Dotazy na velikost tabulky
Jeden způsob, jak identifikovat místa a řádky, které spotřebovávají tabulku v každé z 60 distribuce, je použití [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Však příkazy DBCC může být poměrně omezení.  Zobrazení dynamické správy (zobrazení dynamické správy) zobrazit více podrobností než příkazy DBCC. Začněte vytvořením tohoto zobrazení.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Souhrnné tabulky místa

Tento dotaz vrací řádky a místa v tabulce.  Umožňuje zjistit které tabulky jsou vaše největší tabulky a jestli jsou kruhového dotazování, replikují, nebo hodnoty hash - distribuován.  Dotaz pro distribuované hash tabulky, zobrazuje sloupec, distribuce.  

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Tabulka místo podle typu distribuce

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Tabulka místo podle indexu typu

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Souhrn distribuční prostoru

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Další postup
Po vytvoření tabulky pro datový sklad, dalším krokem je načíst data do tabulky.  Načítání kurzu, najdete v části [načítání dat do SQL Data Warehouse](load-data-wideworldimportersdw.md).