---
title: Návrh tabulky – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Úvod do navrhování tabulek ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: f09b9a93956c9d23e17c742c5f6ec4730591933b
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302309"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Navrhování tabulek ve službě Azure SQL Data Warehouse

Přečtěte si klíčové koncepty pro navrhování tabulek ve službě Azure SQL Data Warehouse. 

## <a name="determine-table-category"></a>Určení kategorie Tabulka 

A [hvězdicového schématu](https://en.wikipedia.org/wiki/Star_schema) organizuje data do tabulek faktů a dimenzí. Některé tabulky se používají pro integraci nebo pracovní data předtím, než se přesune do tabulky faktů nebo dimenze. Při návrhu tabulku, rozhodněte, jestli tabulkových dat patří ve skutečnosti, dimenze nebo tabulky integrace. Toto rozhodnutí informuje struktura příslušné tabulky a distribuci. 

- **Tabulky faktů** obsahovat kvantitativní data, která se běžně vygenerované v rámci transakčním systému a pak načíst do datového skladu. Maloobchodní prodej například generuje prodejní transakce každý den a pak načte data do tabulky faktů datového skladu pro účely analýzy.

- **Tabulky dimenzí** obsahovat atribut data, která se může změnit, ale obvykle mění jen zřídka. Například jméno zákazníka a adresa jsou uložena v tabulce dimenze a aktualizovat pouze, když změní zákazníka profilu. Chcete-li minimalizovat velikost velkou tabulku faktů, název a adresu zákazníka nemusíte být v každém řádku tabulky faktů. Místo toho tabulku faktů a dimenzí tabulky můžete sdílet ID zákazníka. Dotaz můžete spojit dvě tabulky pro přiřazení profilu zákazníka a transakce. 

- **Integrace tabulky** poskytují místo pro integrace a pracovní data. Můžete vytvořit tabulku integrace jako o běžnou tabulku, externí tabulky nebo dočasnou tabulku. Například můžete načíst data do pracovní tabulky, provádět transformace dat v testovacím prostředí a vložte data do provozní tabulky.

## <a name="schema-and-table-names"></a>Schéma a tabulku názvů.
Ve službě SQL Data Warehouse datový sklad je typem databáze. Všechny tabulky v datovém skladu jsou obsaženy v rámci stejné databáze.  Tabulky nelze spojit napříč více datových skladů. Toto chování se liší od SQL serveru, který podporuje spojování napříč databázemi. 

V databázi serveru SQL Server může použít skutečnosti, dimenze, nebo můžete integrovat pro názvy schémat. Pokud provádíte migraci do databáze SQL serveru do služby SQL Data Warehouse, funguje nejlépe migrovat všechny tabulky faktů, dimenzí a integrace jedno schéma v SQL Data Warehouse. Například může ukládat všechny tabulky v [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) ukázkový datový sklad v rámci volá proceduru wwi jedno schéma. Následující kód vytvoří [uživatelem definované schéma](/sql/t-sql/statements/create-schema-transact-sql) volá proceduru wwi.

```sql
CREATE SCHEMA wwi;
```

Chcete-li zobrazit uspořádání tabulek v SQL Data Warehouse, že můžete použít jako předpony, jež mají názvy tabulek faktů, dimenze a int. V následující tabulce jsou uvedeny některé názvy schéma a tabulku pro WideWorldImportersDW. Porovná názvy v systému SQL Server s názvy ve službě SQL Data Warehouse. 

| WideWorldImportersDW tabulky  | Typ tabulky | SQL Server | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| Město | Dimenze | Dimension.City | wwi.DimCity |
| Objednání | Fakt | Fact.Order | wwi.FactOrder |


## <a name="table-persistence"></a>Tabulka trvalosti 

Tabulky ukládat data trvale ve službě Azure Storage, dočasně ve službě Azure Storage nebo v úložišti dat pro datový sklad externí.

### <a name="regular-table"></a>Běžnou tabulku

O běžnou tabulku ukládá data ve službě Azure Storage v rámci datového skladu. V tabulce a data zachová bez ohledu na to, jestli je otevřít relaci.  Tento příklad vytvoří o běžnou tabulku se dvěma sloupci. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Dočasné tabulky
Dočasná tabulka existuje pouze po dobu trvání relace. Dočasnou tabulku můžete použít jiné účely zabráníte zobrazovat výsledky dočasné a také omezit pro vyčištění.  Protože dočasné tabulky také používat místní úložiště, vyšší výkon pro některé operace můžou nabídnout.  Další informace najdete v tématu [dočasné tabulky](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Externí tabulka
Externí tabulka odkazuje na data umístěná v objektu blob Azure Storage nebo Azure Data Lake Store. Při použití ve spojení s příkazem CREATE TABLE AS SELECT, výběr z externí tabulky naimportuje data do SQL Data Warehouse. Proto jsou užitečné pro načítání dat externí tabulky. Kurz načítání najdete v tématu [použití PolyBase k načítání dat z Azure blob storage](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Typy dat
SQL Data Warehouse podporuje nejběžněji používané datové typy. Seznam podporované datové typy najdete v tématu [datové typy v odkazu na tabulku vytvořte](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) v příkazu CREATE TABLE. Minimalizovat velikost datových typů pomáhá zlepšit výkon dotazů. Pokyny k používání datových typů najdete v tématu [datové typy](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Distribuované tabulky
Základní funkce služby SQL Data Warehouse je způsob, jak můžete ukládat a pracovat s tabulkami přes 60 [distribuce](massively-parallel-processing-mpp-architecture.md#distributions).  Tabulky jsou distribuované metodou kruhového dotazování, hodnoty hash nebo replikace.

### <a name="hash-distributed-tables"></a>Hodnoty hash distribuované tabulky
Použijete distribuci hash distribuuje řádků na základě hodnoty ve sloupci distribuce. Tabulka hash distribuované je navržen pro vysoký výkon pro dotaz propojuje na velké tabulky. Existuje několik faktorů, které ovlivňují výběr distribučního sloupce. 

Další informace najdete v tématu [pro distribuované tabulky s pokyny k návrhu](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Replikované tabulky
Replikované tabulky má úplnou kopii tabulky, které jsou k dispozici na všech výpočetních uzlů. Rychle se spouštět dotazy replikovaných tabulkách, protože spojení v replikovaných tabulkách nevyžadují, aby přesun dat. Replikace vyžaduje dodatečné úložiště a není praktické pro velké tabulky. 

Další informace najdete v tématu [pro replikované tabulky s pokyny k návrhu](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Kruhové dotazování tabulky
Řádky tabulky kruhové dotazování tabulky rovnoměrně distribuuje mezi všechny distribuce. Řádky se distribuují náhodně. Načítání dat do kruhové dotazování tabulky je rychlá.  Dotazy však může vyžadovat další přesuny dat než jiné metody distribuce. 

Další informace najdete v tématu [pro distribuované tabulky s pokyny k návrhu](sql-data-warehouse-tables-distribute.md).


### <a name="common-distribution-methods-for-tables"></a>Běžné metody distribuce pro tabulky
Tabulka kategorie často určuje, kterou možnost zvolte pro distribuci v tabulce. 

| Tabulky kategorií | Možnost doporučené distribuce |
|:---------------|:--------------------|
| Fakt           | Použijte distribuci algoritmus hash s clusterovaným indexem columnstore. Dvě tabulky hash jsou připojená na stejný sloupec distribuce zlepšuje výkon. |
| Dimenze      | Použití replikuje menší tabulky. Pokud tabulky je příliš velký pro uložení na jednotlivých výpočetních uzlech, pomocí algoritmu hash distribuce. |
| Fázování        | Pro pracovní tabulku použijte kruhové dotazování. Zatížení pomocí příkazu CTAS je rychlá. Jakmile jsou data v pracovní tabulce, použijte INSERT... Vyberte pro přesun dat do produkčních tabulek. |

## <a name="table-partitions"></a>Oddíly tabulky
Dělenou tabulku ukládá a provede operace s řádky tabulky podle oblasti dat. Například tabulky může být dělené podle dne, měsíce nebo roku. Může zlepšit výkon dotazů pomocí eliminace oddílů, který omezuje dotazu prohledávání dat v rámci oddílu. Můžete také spravovat dat prostřednictvím přepínání oddílů. Protože je již distribuovaných dat ve službě SQL Data Warehouse, příliš mnoho oddílů může zpomalit výkon dotazů. Další informace najdete v tématu [pokyny k dělení](sql-data-warehouse-tables-partition.md).

## <a name="columnstore-indexes"></a>Indexy Columnstore
Ve výchozím nastavení SQL Data Warehouse ukládá tabulku jako s clusterovaným indexem columnstore. Tato forma úložiště dat dosahuje vysoké údaje komprese při přenosu a výkonu dotazování u velkých tabulek.  Clusterovaný index columnstore je většinou nejlepší volbou, ale v některých případech je clusterovaný index nebo haldu vhodnou strukturu.

Seznam funkcí columnstore, naleznete v tématu [co je nového v případě indexů columnstore](/sql/relational-databases/indexes/columnstore-indexes-whats-new). Ke zlepšení výkonu index columnstore, naleznete v tématu [maximalizuje kvality skupiny řádků v případě indexů columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statistika
Optimalizace dotazů používá Statistika na úrovni sloupce při vytváření plánu pro provedení dotazu. Pokud chcete zlepšit výkon dotazů, je důležité vytvořit statistiku pro jednotlivé sloupce, zejména sloupce použité v dotazu spojení. Vytváření a aktualizaci statistik neprobíhá automaticky. [Vytvoření statistiky](/sql/t-sql/statements/create-statistics-transact-sql) po vytvoření tabulky. Aktualizujte statistiku po velký počet řádků jsou přidány nebo změněny. Například aktualizujte statistiku po zatížení. Další informace najdete v tématu [statistiky pokyny](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Příkazů pro vytvoření tabulky
Vytvořit tabulku jako nové prázdné tabulky. Můžete také vytvořit a vyplnit tabulku s výsledky příkazu select. Dále jsou uvedené příkazy jazyka T-SQL pro vytvoření tabulky.

| Příkaz jazyka T-SQL | Popis |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Vytvoří prázdnou tabulku tak, že definujete všechny sloupce tabulky a možnosti. |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) | Vytvoří externí tabulku. Definice tabulky se ukládají ve službě SQL Data Warehouse. Data v tabulce je uložená v Azure Blob storage nebo Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Naplní novou tabulku s výsledky příkazu select. Tabulka sloupce a datové typy jsou založeny na výsledky příkazu select. K importu dat, tento příkaz můžete vybrat z externí tabulky. |
| [VYTVOŘIT EXTERNÍ TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Vytvoří novou externí tabulku tak, že vyexportujete výsledky příkazu select na externím místě.  Umístění je Azure Blob storage nebo Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Zarovnávání dat zdroje k datovému skladu

Načtením dat z jiného zdroje dat se vyplní tabulky datového skladu. Pokud chcete provést úspěšné načtení, čísla a datové typy sloupců ve zdrojových datech musí být v souladu s definicí tabulky v datovém skladu. Získání dat pro zarovnání může být Nejtěžší částí navrhování tabulek. 

Pokud data pocházejí z více úložišť dat, můžete přenést data do datového skladu a uložit v tabulce integrace. Jakmile budou data v tabulce integraci, můžete provádět operace transformace výkon služby SQL Data Warehouse. Jakmile je připraven data, vložíte ho do produkčních tabulek.

## <a name="unsupported-table-features"></a>Nepodporovaná tabulka funkce
SQL Data Warehouse podporuje mnoho, ale ne všechny funkce tabulky nabízené ostatní databáze.  V následujícím seznamu jsou uvedeny některé tabulky funkcí, které nejsou podporované ve službě SQL Data Warehouse.

- Primární klíč, cizí klíče, zkontrolujte, jedinečný [omezení tabulek](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [Vypočítané sloupce](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Indexovaná zobrazení](/sql/relational-databases/views/create-indexed-views)
- [Pořadí](/sql/t-sql/statements/create-sequence-transact-sql)
- [Zhuštěný sloupec](/sql/relational-databases/tables/use-sparse-columns)
- [Náhradní klíče](). Implementace s [Identity](sql-data-warehouse-tables-identity.md).
- [Synonyma](/sql/t-sql/statements/create-synonym-transact-sql)
- [Triggery](/sql/t-sql/statements/create-trigger-transact-sql)
- [Jedinečné indexy](/sql/t-sql/statements/create-index-transact-sql)
- [Uživatelem definované typy](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Dotazy na velikost tabulky
Jeden jednoduchý způsob, jak identifikovat místa a řádky používané tabulce v každém 60 distribucí je použít [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Nicméně používání příkazů DBCC můžou být poměrně omezující.  Zobrazení dynamické správy (DMV) zobrazit podrobnější informace než příkazy DBCC. Začněte vytvořením tohoto zobrazení.

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

### <a name="table-space-summary"></a>Souhrn místa na tabulku

Tento dotaz vrátí řádky a místo v tabulce.  Umožňuje vám informace, které tabulky jsou největší tabulky a určuje, zda jsou kruhové dotazování, replikaci, nebo hodnoty hash - distribuovány.  Pro hodnoty hash distribuované tabulky dotaz zobrazí sloupec distribuce.  

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

### <a name="table-space-by-distribution-type"></a>Tabulkový prostor podle typu distribuce

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

### <a name="table-space-by-index-type"></a>Tabulkový prostor typem indexu

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

### <a name="distribution-space-summary"></a>Souhrn distribučního místa

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
Po vytvoření tabulek pro váš datový sklad, dalším krokem je načtení dat do tabulky.  Kurz načítání najdete v tématu [načítání dat do SQL Data Warehouse](load-data-wideworldimportersdw.md).