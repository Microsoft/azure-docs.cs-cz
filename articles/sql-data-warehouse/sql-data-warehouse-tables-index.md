---
title: Indexování tabulky ve službě Azure SQL Data Warehouse | Microsoft Azure
description: Doporučení a příklady pro indexování tabulky ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d709acfe378583a21b72971f465e4b5d73818bcd
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307724"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexování tabulky ve službě SQL Data Warehouse
Doporučení a příklady pro indexování tabulky ve službě Azure SQL Data Warehouse.

## <a name="what-are-index-choices"></a>Jaké jsou možnosti index?

SQL Data Warehouse nabízí několik možností indexování včetně [Clusterované indexy columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview), [Clusterované indexy a neclusterovaných indexů](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described), a také možnost bez index [haldy ](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Vytvořit tabulku s indexem, najdete v článku [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) dokumentaci.

## <a name="clustered-columnstore-indexes"></a>Clusterované indexy columnstore
Ve výchozím nastavení vytvoří SQL Data Warehouse při nejsou zadány žádné parametry indexu u tabulky s clusterovaným indexem columnstore. Clusterované tabulky columnstore nabízí nejvyšší úroveň komprese dat i nejlepší výkon dotazů.  Clusterované tabulky columnstore budou obecně překonat clusterovaný index nebo haldy tabulek a jsou obvykle nejlepší volbou pro velké tabulky.  Z těchto důvodů Clusterované columnstore je nejlepším místem, kde můžete spustit, když si nejste jistí, jak indexování tabulky.  

Pokud chcete vytvořit Clusterované tabulky columnstore, jednoduše zadejte CLUSTEROVANÉHO indexu COLUMNSTORE v klauzuli WITH nebo nechte pole v klauzuli WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Zde je několik scénářů, kde Clusterované columnstore nemusí být vhodný:

- Tabulky Columnstore nepodporuje varchar(max), nvarchar(max) a varbinary(max). Místo toho zvažte haldy nebo clusterovaný index.
- Tabulky Columnstore je možné pro přechodná data méně efektivní. Vezměte v úvahu haldy a možná i dočasné tabulky.
- Malé tabulky s méně než 100 milionů řádků. Vezměte v úvahu tabulky haldy.

## <a name="heap-tables"></a>Tabulky hald
Když dočasně umisťujete data ve službě SQL Data Warehouse, můžete zjistit, že použitím tabulky haldy je celý proces zrychlí. Je to proto, že do hald načítají rychleji než do tabulky indexů a v některých případech následné čtení se dá udělat z mezipaměti.  Pokud nahráváte data pouze za účelem jejich přípravy před spuštěním dalších transformací, nahrání tabulky do tabulky haldy je mnohem rychlejší než nahrání dat do Clusterované tabulky columnstore. Kromě toho nahrání dat do [dočasnou tabulku](sql-data-warehouse-tables-temporary.md) načte rychlejší, než nahrání tabulky do trvalého úložiště.  

Pro malé vyhledávacími tabulkami, méně než 100 milionů řádků tabulky hald často má smysl.  Začít dosáhli optimální komprese po více než 100 milionů řádků tabulky columnstore clusteru.

K vytvoření tabulky haldy, zadejte jednoduše HALDY v klauzuli WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Clusterovaných a neclusterovaných indexů
Clusterované indexy může překonat clusterovaných tabulek columnstore, když potřebuje rychle načíst jeden řádek. Pro dotazy, kde je potřeba výkonu s extrémně rychlostí jednu nebo velmi málo řádků vyhledávání vezměte v úvahu index clusteru nebo sekundární neclusterovaný index. Nevýhodou použití clusterovaného indexu je, že pouze dotazy, které přináší výhody jsou ty, které používají vysoce selektivní filtr na sloupec clusterovaný index. K vylepšení filtru na ostatní sloupce lze přidat neclusterovaný index na jiné sloupce. Ale každý index, který je přidán do tabulky přidá prostor i čas zpracování zátěže.

Pokud chcete vytvořit clusterovaný index tabulky, zadejte jednoduše CLUSTEROVANÉHO indexu v klauzuli WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Chcete-li přidat neclusterovaný index u tabulky, jednoduše použijte následující syntaxi:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimalizace Clusterované indexy columnstore
Clusterované tabulky columnstore jsou uspořádány do dat do segmentů.  Segment vysoké kvality, je nezbytné k zajištění optimální výkon dotazů na tabulky columnstore.  Kvalitu segmentů lze změřit podle počtu řádků v komprimované řádek skupiny.  Kvality segmentů je optimální, pokud existuje alespoň 100 tisíc řádků na každém řádku komprimované skupině a získat výkonu jako počet řádků na koncepci skupin řádků 1 048 576 řádků, což je většina řádky, které mohou obsahovat skupinu řádků.

Níže zobrazení můžete vytvořit a použít ve vašem systému pro výpočet průměrné řádků na každém řádku seskupovat a identifikovat žádné indexy columnstore neoptimálním průběhem clusteru.  Poslední sloupec v tomto zobrazení generuje příkazu SQL, který slouží k opětovnému sestavení indexů.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Teď, když jste vytvořili zobrazení, spusťte tento dotaz k identifikaci se skupinami řádků tabulky s méně než 100 tisíc řádků. Samozřejmě můžete chtít zvýšit prahovou hodnotu 100 tisíc, pokud hledáte další kvality segmentů optimální. 

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Po spuštění dotazu, který můžete začít, podívejte se na data a analyzovat výsledky. Tato tabulka vysvětluje, co se má hledat v analýze skupiny řádků.

| Sloupec | Použití těchto dat |
| --- | --- |
| [table_partition_count] |Pokud je tabulka rozdělená na oddíly, pak může očekáváte vidět, že počítá vyšší skupinu otevřete řádků. Každý oddíl v distribuci teoreticky mít skupinu otevřete řádek s ním spojená. Faktor to do analýzy. Malé tabulky, který se rozdělil může optimalizovat tak, že odeberete dělení úplně jako by to zlepšit komprese. |
| [row_count_total] |Celkový počet řádků tabulky. Tuto hodnotu můžete například použít k výpočtu procentuální podíl řádků v komprimovaném stavu. |
| [row_count_per_distribution_MAX] |Pokud jsou všechny řádky rovnoměrně distribuovaných tato hodnota by cílový počet řádků na distribuci. Porovnejte tuto hodnotu compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Celkový počet řádků ve formátu columnstore pro tabulku. |
| [COMPRESSED_rowgroup_rows_AVG] |Pokud průměrný počet řádků je výrazně menší než maximální počet řádků pro skupinu řádků, zvažte použití příkazu CTAS nebo ALTER INDEX REBUILD pro opětovnou data |
| [COMPRESSED_rowgroup_count] |Počet skupin řádků ve formátu columnstore. Pokud toto číslo je velmi vysoké ve vztahu k tabulce je indikátor, že je nízká hustota columnstore. |
| [COMPRESSED_rowgroup_rows_DELETED] |Řádky jsou logicky odstraněny ve formátu columnstore. Pokud je číslo vysokou vzhledem k velikosti tabulky, zvažte opětovné vytvoření oddílu nebo znovu sestavit index, protože se odebere fyzicky. |
| [COMPRESSED_rowgroup_rows_MIN] |Toto použijte ve spojení s Průměrný a maximální počet sloupců porozumět rozsahu hodnot pro skupiny řádků ve vašich columnstore. Nízké číslo nad prahovou hodnotou zatížení (102,400 na distribuci zarovnání oddílů) naznačuje, že optimalizace jsou k dispozici v načtení dat |
| [COMPRESSED_rowgroup_rows_MAX] |Stejně jako výše |
| [OPEN_rowgroup_count] |Otevřené skupiny řádků je normální. Jeden rozumně byste očekávali jednu skupinu OTEVŘETE řádek na distribuce tabulky (60). Nadměrné čísla navrhnout data načítání napříč oddíly. Dvojitá kontrola strategie dělení a ujistěte se, zda je zvukový |
| [OPEN_rowgroup_rows] |Každá skupina řádků může mít 1 048 576 řádků v něm jako maximální. Tuto hodnotu použijte, chcete-li zobrazit, jak otevřené skupiny řádků jsou v tuto chvíli |
| [OPEN_rowgroup_rows_MIN] |Otevření skupin znamenat, že data jsou skapat načítání do tabulky nebo že předchozí zatížení přesahovat zbývající řádky do této skupiny řádků. Použít MIN, MAX, AVG sloupce, které chcete zobrazit, kolik dat je ne v programu OPEN řádek skupiny. Pro malé tabulky je možné, 100 % všech dat! V takovém případě ALTER INDEX REBUILD vynutí data columnstore. |
| [OPEN_rowgroup_rows_MAX] |Stejně jako výše |
| [OPEN_rowgroup_rows_AVG] |Stejně jako výše |
| [CLOSED_rowgroup_rows] |Podívejte se na seskupení řádků uzavřené řádek jako kontrolu správnosti. |
| [CLOSED_rowgroup_count] |Počet skupin uzavřené řádku by měl být nízká, pokud některý viděli vůbec. Skupiny uzavřené řádků je možné převést na komprimovaná skupina řádků pomocí příkazu ALTER INDEX... Příkaz REORGANIZOVAT. Není to ale obvykle vyžaduje. Uzavřené skupiny se automaticky převedou na skupiny řádků columnstore "přesunu řazené kolekce členů" procesem na pozadí. |
| [CLOSED_rowgroup_rows_MIN] |Skupiny uzavřené řádků by měl mít výplně velmi vysoké míry. Pokud je míra naplnění pro skupinu uzavřené řádků s nízkou, vyžaduje se další analýza je index columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Stejně jako výše |
| [CLOSED_rowgroup_rows_AVG] |Stejně jako výše |
| [Rebuild_Index_SQL] |Chcete-li znovu sestavit index columnstore pro tabulku SQL |

## <a name="causes-of-poor-columnstore-index-quality"></a>Způsobí, že špatná columnstore index kvality
Pokud jste našli tabulky s kvality segmentů nízký, budete chtít zjistit původní příčinu.  Níže jsou uvedeny některé běžné příčiny nízký segmentu kvality:

1. Přetížení paměti, když byl sestaven indexu
2. Velký objem operací DML
3. Malé nebo skapat operace načítání
4. Příliš mnoho oddílů

Tyto faktory mohou způsobit index columnstore, aby výrazně méně než optimální 1 milion řádků na skupinu řádků. Může také dojít k tomu řádků, které mají přejít na skupinu řádků delta místo skupiny komprimovaný řádek. 

### <a name="memory-pressure-when-index-was-built"></a>Přetížení paměti, když byl sestaven indexu
Počet řádků na skupinu komprimovaný řádek přímo souvisí šířky řádku a množství paměti k dispozici pro zpracování skupinu řádků.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Osvědčeným postupem je proto poskytnout relace, která zapisuje do indexu columnstore tabulky přístup k tolik paměti co nejvíce.  Protože kompromis mezi pamětí a souběžnosti, pokyny k přidělení paměti správné závisí na data v jednotlivých řádcích tabulky, jednotky datového skladu, které jsou přiděleny do vašeho systému a slotů souběžnosti, které poskytnete k relaci který zapisuje data do tabulky.  Jako osvědčený postup doporučujeme začít s xlargerc, pokud používáte DW300 nebo méně largerc, pokud používáte DW400 na úroveň DW600 a mediumrc, pokud používáte DW1000 a vyšší.

### <a name="high-volume-of-dml-operations"></a>Velký objem operací DML
Velký objem operací DML, aktualizovat a odstraňovat řádky můžete zavést nedostatků do indexu columnstore. To platí zejména při změně většinou řádků v skupinu řádků.

- Odstranění řádku ze skupiny komprimovaný řádek pouze logicky řádek bude označen jako odstraněný. Řádek zůstává ve skupině komprimovaný řádek, dokud znovu sestavit na oddíl nebo tabulky.
- Vložíte řádek se přidá řádek do tabulky interní rowstore názvem skupiny řádků delta. Vložené: řádek není převedena na columnstore, dokud skupina řádků rozdílů je plný a je označené jako zavřené. Skupiny řádků zavřou, když dosáhnou maximální kapacitu 1 048 576 řádků. 
- Aktualizace řádku ve formátu columnstore je zpracován jako logické delete a potom insert. Vloženého řádku mohou být uloženy v úložišti delta.

Dávkové aktualizace a operací vložení, které by překračovaly prahovou hodnotu hromadné 102 400 řádků na zarovnání oddílu distribuci přejít přímo do formátu columnstore. Ale za předpokladu, že rovnoměrná distribuce, musíte být upravit 6.144 milionů řádků v rámci jedné operace to dělo. Pokud počet řádků pro daný oddíl bočně zarovnaných distribuci je menší než 102,400 řádky pokračujte andstay obchodu rozdílovou existuje dokud dostatečná řádků bylo vloženo nebo upravit tak, aby zavřít skupinu řádků nebo po opětovném sestavení indexu.

### <a name="small-or-trickle-load-operations"></a>Malé nebo skapat operace načítání
Malé načte, že tok do SQL Data Warehouse se také někdy označované jako skapat zatížení. Obvykle představují téměř nepřetržitý datový proud dat se přijatý systémem. Ale protože tento datový proud se blíží průběžné svazek řádků není zejména velkých. Častěji data je výrazně pod prahovou hodnotou. vyžaduje se pro přímé načítání do formátu columnstore.

V těchto situacích je často vhodnější nejprve dostat data do úložiště objektů blob v Azure a ten accumulate před načtením. Tato technika se často označuje jako *dávkování micro*.

### <a name="too-many-partitions"></a>Příliš mnoho oddílů
Další věc, které byste měli zvážit je vlivu dělení na vaše clusterovaných tabulek columnstore.  Před dělení, SQL Data Warehouse již rozděluje data do 60 databází.  Dělení, rozdělí se další data.  Pokud svá data dělit, zvažte, který **každý** oddílu potřebuje aspoň 1 milion řádků, abyste využili výhod clusterovaného indexu columnstore.  Pokud jste ji rozdělit do oddílů do 100 oddílů, pak tabulce potřebuje alespoň 6 miliard řádků, abyste využili výhod clusterovaného indexu columnstore (60 distribucí * 100 oddílů * 1 milion řádků). Pokud vaše tabulka 100 oddílů neobsahuje 6 miliard řádků, buď snižte počet oddílů, nebo zvažte místo toho použití tabulky haldy.

Po načtení tabulky s daty postupujte níže uvedený postup k identifikaci a znovu vytvořit tabulky s neoptimálním průběhem Clusterované indexy columnstore.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Nové sestavení indexů ke zlepšení kvality segmentů
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Krok 1: Určete nebo vytvořte uživatele, který používá správný prostředek třídy
Rychlý způsob okamžitě zlepšení kvality segmentů je index znovu sestavit.  SQL vrácený zobrazení výše vrátí příkaz ALTER INDEX REBUILD, který slouží k opětovnému sestavení indexů. Při opětovném sestavování indexů, ujistěte se, že přidělíte dostatek paměti pro relaci, která znovu sestaví indexy.  K tomuto účelu zvýšit Třída prostředků uživatele, který má oprávnění k opětovnému sestavení indexů v této tabulce na Doporučená minimální. Třída prostředků uživatele vlastníka databáze nemůže být změněna, takže pokud jste ještě nevytvořili uživatele v systému, musíte nejdříve provést. Minimální doporučená nastavení je třída prostředků xlargerc Pokud používáte DW300 nebo méně largerc Pokud používáte DW400 na úroveň DW600 a mediumrc, pokud používáte DW1000 a vyšší.

Níže je příklad toho, jak přidělit víc paměti uživateli zvýšením své třídy prostředků. Práce s třídami prostředků, najdete v článku [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Krok 2: Znovu vytvořit Clusterované indexy columnstore s větším uživatelském třídy prostředků
Přihlaste se jako uživatel z kroku 1 (třeba LoadUser), která je teď vyšší třídě prostředků, a spusťte příkazy ALTER INDEX. Ujistěte se, že tento uživatel má oprávnění ALTER na tabulky, kde index je znovu sestaven. Tyto příklady ukazují, jak znovu sestavte index columnstore celý nebo znovu sestavit jeden oddíl. U velkých tabulek je víc praktických znovu sestavit indexy jeden oddíl v čase.

Místo znovu sestavit index, může případně zkopírujte tabulku, do nové tabulky [použití příkazu CTAS](sql-data-warehouse-develop-ctas.md). Jakým způsobem je nejvhodnější? Pro velké objemy dat, je obvykle rychlejší než CTAS [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). Pro menší objem dat ALTER INDEX se snadněji používá a není třeba vyměnit v tabulce. Zobrazit **nové sestavení indexů CTAS a přepínání oddílů** níže podrobné informace o tom, jak provést nové sestavení indexů s CTAS.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Nové sestavení indexu ve službě SQL Data Warehouse je v režimu offline operace.  Další informace o nové sestavení indexů, naleznete v tématu v části ALTER INDEX REBUILD [defragmentace indexy Columnstore](/sql/relational-databases/indexes/columnstore-indexes-defragmentation), a [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Krok 3: Ověření, že se zlepšila kvalita segmentů columnstore clusteru
Opětovné spuštění dotazu, které identifikované tabulky s špatné segmentovat kvality a ověření kvality segmentů zvýšil.  Pokud ke zlepšení kvality segmentů, je možné, že jsou velmi široké řádky v tabulce.  Zvažte použití vyšší třídě prostředků nebo DWU, když nové sestavení indexů.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Nové sestavení indexů CTAS a přepínání oddílů
V tomto příkladu [vytvořit TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) prohlášení a přepnutí opětovné sestavení oddílů tabulky oddílu. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Create a SWITCH out table
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Další podrobnosti o opětovné vytvoření oddílů použití příkazu CTAS najdete v tématu [používání oddílů ve službě SQL Data Warehouse](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Další postup
Další informace o vývoji tabulky, najdete v části [vývoj tabulek](sql-data-warehouse-tables-overview.md).

