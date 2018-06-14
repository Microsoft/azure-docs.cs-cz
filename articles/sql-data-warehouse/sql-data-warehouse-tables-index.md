---
title: Indexování tabulky v Azure SQL Data Warehouse | Microsoft Azure
description: Doporučení a příklady pro indexování tabulky v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 75d3638326bc1bf2f72997fa9d5d5feabc837a62
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527150"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexování tabulek v SQL Data Warehouse
Doporučení a příklady pro indexování tabulky v Azure SQL Data Warehouse.

## <a name="what-are-index-choices"></a>Jaké jsou možnosti indexu?

SQL Data Warehouse nabízí několik možností indexování včetně [Clusterované indexy columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview), [Clusterované indexy a neclusterované indexy](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described), a také známé jako možnost jiný index [haldy ](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Chcete-li vytvořit tabulku s indexem, přečtěte si téma [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) dokumentaci.

## <a name="clustered-columnstore-indexes"></a>Clusterované indexy columnstore
Ve výchozím nastavení vytvoří SQL Data Warehouse clusterovaný index columnstore při nejsou zadány žádné možnosti indexu v tabulce. Clusterované tabulky columnstore nabízejí nejvyšší úrovně komprese dat jak nejlepší celkový výkon dotazů.  Clusterované tabulky columnstore se obecně překonat clusterovaný index nebo haldy tabulek a jsou většinou nejlepší volbou pro rozsáhlé tabulky.  Z těchto důvodů Clusterové columnstore je nejlepší místo k spustit v případě, že si nejste jistí, jak index tabulku.  

K vytvoření clusteru columnstore tabulky, jednoduše zadejte CLUSTEROVANÝ INDEX COLUMNSTORE v klauzuli WITH nebo ponechat v klauzuli WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Existuje několik situací, kde Clusterové columnstore nemusí být vhodný:

- Tabulky Columnstore nepodporují, varchar(max), nvarchar(max) a varbinary(max). Místo toho zvažte haldy nebo clusterovaný index.
- Tabulky Columnstore může být méně efektivní pro přechodný data. Zvažte haldy a možná i dočasných tabulek.
- Malé tabulky s méně než 100 milionu řádků. Vezměte v úvahu haldy tabulky.

## <a name="heap-tables"></a>Tabulky haldy
Pokud data jsou dočasně cílová v SQL Data Warehouse, můžete zjistit, že pomocí tabulky haldy umožňuje rychlejší celý proces. To je proto zatížení do hald je rychlejší než indexu tabulky a v některých případech lze provést další čtení z mezipaměti.  Pokud jsou načítání dat pouze na Příprava před spuštěním další transformace, načítání tabulky do tabulky haldy je mnohem rychlejší než načítání dat do tabulky columnstore clusteru. Kromě toho načítání dat do [dočasné tabulky](sql-data-warehouse-tables-temporary.md) načte rychleji než načítání tabulky trvalém úložišti.  

Pro malé vyhledávací tabulky, méně než 100 miliónů řádky, často haldy tabulky smysl.  Tabulky columnstore clusteru začít zajistit optimální komprese, jakmile bude existovat více než 100 milionu řádků.

K vytvoření tabulky haldy, jednoduše zadejte HALDY v klauzuli WITH:

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
Clusterované indexy může překonat Clusterované tabulky columnstore, když jeden řádek musí být rychle načíst. Pro dotazy, kde je potřeba výkonu s extrémně rychlost jednu, nebo jen několik řádek vyhledávání zvažte index clusteru nebo sekundární neclusterovaný index. Nevýhodou pomocí clusterovaného indexu je, že pouze na dotazy využívající jsou ty, které vysoce selektivní filtr použít u sloupce clusterovaného indexu. Pro zlepšení filtru na ostatních sloupců neclusterovaný index lze přidat na jiné sloupce. Každý index, která se přidá do tabulky však přidá místa a doba zpracování pro zatížení.

Pokud chcete vytvořit clusterovaný index tabulky, jednoduše zadejte CLUSTEROVANÝ INDEX v klauzuli WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Pokud chcete přidat neclusterovaný index v tabulce, jednoduše použijte následující syntaxi:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimalizace Clusterované indexy columnstore
Clusterované tabulky columnstore jsou uspořádány v datech do segmentů.  S vysokou segment kvality je velmi důležité k dosažení výkonu optimální dotazu na tabulky columnstore.  Počet řádků ve skupině komprimované řádek lze měřit kvalitu segmentu.  Segment kvality je optimální, kde existují nejméně 100 tisíc řádků na jeden řádek komprimované skupiny a získat výkonu jako počet řádků na jeden řádek koncepci skupin 1 048 576 řádků, což je většina řádky, které může obsahovat skupiny řádků.

Níže zobrazení můžete vytvořit a použít v systému k výpočtu průměrné řádků na jeden řádek, skupiny a identifikovat žádné indexy columnstore neoptimálním průběhem clusteru.  Poslední sloupec v tomto zobrazení generuje příkazu SQL, který můžete použít k opětovnému sestavení indexů.

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

Teď, když jste vytvořili zobrazení, spusťte tento dotaz k identifikaci tabulek se skupiny řádků s méně než 100 tisíc řádků. Samozřejmě můžete zvýšit prahovou hodnotu 100 tisíc Pokud hledáte další kvality optimální segmentu. 

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Po spuštění dotazu, který můžete začít podívejte se na data a analyzujte výsledky. Tato tabulka vysvětluje, co má být vyhledán analýzou skupiny řádků.

| Sloupec | Jak používat tato data |
| --- | --- |
| [table_partition_count] |Pokud tabulka je rozdělena na oddíly, pak byste může měli vidět, že počty vyšší skupiny otevřete řádků. Každý oddíl v distribuci může mít teoreticky skupinu otevřete řádek s ním spojená. To dvoufaktorového do analýzy. Malé tabulky, který byl rozdělený do oddílů může optimalizovat odebráním dělení zcela to by zlepšilo komprese. |
| [row_count_total] |Celkový počet řádků tabulky. Například můžete tuto hodnotu vypočítat podíl řádků v komprimované stavu. |
| [row_count_per_distribution_MAX] |Pokud všechny řádky jsou rovnoměrně rozmístěny. Tato hodnota by cílový počet řádků na jeden distribuce. Tato hodnota se compressed_rowgroup_count porovnejte. |
| [COMPRESSED_rowgroup_rows] |Celkový počet řádků ve formátu columnstore pro tabulku. |
| [COMPRESSED_rowgroup_rows_AVG] |Průměrný počet řádků je výrazně menší než maximální počet řádků pro skupinu řádek, můžete použít funkce CTAS nebo ALTER INDEX REBUILD pro opětovnou data |
| [COMPRESSED_rowgroup_count] |Počet skupin řádků ve formátu columnstore. Pokud toto číslo je velmi vysoká ve vztahu k tabulce je indikátor nedostatku hustotu columnstore. |
| [COMPRESSED_rowgroup_rows_DELETED] |Ve formátu columnstore jsou logicky odstraněných řádků. Pokud je číslo vysoké vzhledem k velikosti tabulky, zvažte opětovného vytvoření oddílu nebo znovu sestavit index, protože se odebere je fyzicky. |
| [COMPRESSED_rowgroup_rows_MIN] |Použijte ve spojení s Průměrný a maximální počet sloupců pochopit rozsahu hodnot pro skupiny řádků ve vaší columnstore. Nízké číslo nadměrného zatížení (102,400 za oddílu zarovnán distribuční) naznačuje, že nejsou k dispozici v načtení dat optimalizace |
| [COMPRESSED_rowgroup_rows_MAX] |Stejně jako výše |
| [OPEN_rowgroup_count] |Skupiny otevřete řádků je normální. To bude přiměřeně jeden by uživatel očekával jednu skupinu OTEVŘETE řádek na distribuce tabulky (60). Nadměrné čísla navrhnout data načítání napříč oddíly. Překontrolujte rozdělení strategie Ujistěte se, zda je zvukový |
| [OPEN_rowgroup_rows] |Každý řádek skupiny může mít 1 048 576 řádků v něm jako maximální. Tato hodnota slouží k uvidíte, jak úplné skupiny otevřete řádků jsou aktuálně |
| [OPEN_rowgroup_rows_MIN] |Otevření skupin znamenat, že data skapat načítá do tabulky nebo že předchozí zatížení uniknout zbývající řádky do této skupiny řádků. Použít minimum, maximum, průměr sloupce chcete zobrazit, kolik dat je byla ve OTEVŘETE řádek skupiny. Pro malé tabulky může být 100 % všechna data! V takovém případě ALTER INDEX REBUILD vynutit data na columnstore. |
| [OPEN_rowgroup_rows_MAX] |Stejně jako výše |
| [OPEN_rowgroup_rows_AVG] |Stejně jako výše |
| [CLOSED_rowgroup_rows] |Podívejte se na seskupení řádků uzavřené řádek jako kontrolu správností. |
| [CLOSED_rowgroup_count] |Počet skupin uzavřené řádků by měl být nízká, pokud žádné vidět vůbec. Uzavřené řádek skupiny lze převést na skupiny komprimované řádku pomocí příkazu ALTER INDEX... Příkaz REORGANIZOVAT. Není to však obvykle vyžaduje. Uzavřené skupiny budou automaticky převedeny na skupiny řádků columnstore procesem "přesunu řazené kolekce členů" pozadí. |
| [CLOSED_rowgroup_rows_MIN] |Uzavřené řádek skupiny musí mít na velmi vysokou výplně míru. Pokud je rychlost výplně pro skupinu uzavřené řádek nízkou, další analýza columnstore je požadovaná. |
| [CLOSED_rowgroup_rows_MAX] |Stejně jako výše |
| [CLOSED_rowgroup_rows_AVG] |Stejně jako výše |
| [Rebuild_Index_SQL] |SQL znovu sestavit index columnstore pro tabulku |

## <a name="causes-of-poor-columnstore-index-quality"></a>Příčiny nízký columnstore index kvality
Pokud jste našli tabulky s kvalitou nízký segmentu, budete chtít určit hlavní příčinu.  Tady jsou některé běžné příčiny nízký segment kvality:

1. Pokud byl vytvořený index nárokům na paměť
2. Velkému počtu operace DML
3. Malá nebo skapat zatížení operací
4. Příliš mnoho oddíly

Tyto faktory mohou způsobit index columnstore tak, aby měl výrazně menší než optimální 1 milionu řádků na skupinu řádků. Také způsobit řádky, které chcete přejít na řádek skupinu rozdílů místo skupiny komprimované řádek. 

### <a name="memory-pressure-when-index-was-built"></a>Pokud byl vytvořený index nárokům na paměť
Počet řádků na skupinu komprimované řádek přímo souvisí s šířka řádku a množství paměti k dispozici pro zpracování skupiny řádků.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Osvědčeným postupem je proto umožnit relace, který je zápis do indexu columnstore tabulky přístup k tolik paměti.  Vzhledem k tomu, že je kompromis mezi paměti a souběžnost, pokyny k přidělení správné paměti závisí na datech v každý řádek tabulky, jednotky datového skladu přidělené systému a počet souběžnosti sloty, které poskytnete relace, který zapisuje data do tabulky.  Jako osvědčený postup doporučujeme začít s xlargerc, pokud používáte DW300 nebo méně largerc, pokud používáte DW400 DW600 a mediumrc, pokud používáte DW1000 a vyšší.

### <a name="high-volume-of-dml-operations"></a>Velkému počtu operace DML
K velkému počtu operace DML, které aktualizace a odstraňování řádků můžou představovat neefektivnost na columnstore. To platí hlavně při změně většina řádky v skupiny řádků.

- Odstranění řádku ze skupiny komprimované řádek pouze logicky označí řádek, jako je odstranit. Řádek zůstane ve skupině komprimované řádek, dokud znovu sestavit oddílu nebo tabulky.
- Na vnitřní vytvořit tabulku nazývá skupina řádek rozdílů vložíte řádek se přidá řádek. Vloženého řádku není převést na columnstore, dokud se skupiny řádků rozdílů je plný a je označené jako zavřené. Skupiny řádků jsou uzavřeny po uplynutí maximální kapacita 1 048 576 řádků. 
- Aktualizace řádků ve formátu columnstore zpracovávány jako logické odstranit a pak vložení. Vloženého řádku může být uložen v úložišti delta.

Dávkovou aktualizací a operace insert, které překročí prahovou hodnotu hromadné činí 102 400 řádků na jeden oddíl zarovnaný distribuční přejít přímo na formát columnstore. Ale za předpokladu, že i distribuce, museli byste být úprava více než 6.144 milionu řádků v rámci jedné operace pro tuto funkci používat. Pokud počet řádků pro daný distribuční zarovnaný oddílu je menší než 102,400 potom řádky přejděte na andstay úložiště rozdílů existuje dokud byl vložen nebo upravit tak, aby zavřete skupiny řádků dostatečná řádků nebo byla znovu sestavena index.

### <a name="small-or-trickle-load-operations"></a>Malá nebo skapat zatížení operací
Malá načte, že tok do SQL Data Warehouse se také někdy označuje jako skapat zatížení. Obvykle představují near nepřetržitý datový proud dat probíhá požita systému. Však jako tento datový proud je téměř průběžné svazku řádků není zvlášť velký. Častěji data je výrazně pod prahovou hodnotou. vyžaduje se pro přímé zatížení do formátu columnstore.

V těchto situacích je často lepší nejprve zobrazovat data v Azure blob storage a nechat ji hromadit před načtením. Tento postup se často označuje jako *dávkování micro*.

### <a name="too-many-partitions"></a>Příliš mnoho oddíly
Jiné věc vzít v úvahu, je vliv na vaše Clusterované tabulky columnstore oddíly.  Před oddílů, SQL Data Warehouse již rozděluje data do 60 databáze.  Vytváření oddílů vydělí další data.  Pokud jste oddílu dat, pak, zvažte **každý** oddílu potřebuje alespoň 1 milionu řádků, abyste mohli využívat výhod clusterovaný index columnstore.  Pokud oddílu tabulku do 100 oddílů, pak tabulku musí alespoň 6 miliardy řádků, abyste mohli využívat výhod s clusterovaným indexem columnstore (60 distribuce * 100 oddíly * 1 milionu řádků). Pokud vaše 100 oddílu tabulka nemá 6 miliardy řádků, snižte počet oddílů nebo zvažte použití haldy tabulku místo.

Jakmile vaše tabulky byly načteny s některá data, níže uvedených pokynů k identifikaci a znovu vytvořit tabulky s neoptimálním průběhem Clusterované indexy columnstore.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Nové sestavení indexů ke zlepšení kvality segmentu
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Krok 1: Určení nebo vytvoření uživatele, který používá třída správné prostředků
Rychlým způsobem okamžitě zlepšení kvality segmentu je znovu sestavte index.  Vrácený výše uvedené zobrazení SQL vrátí příkaz ALTER INDEX REBUILD, který můžete použít k opětovnému sestavení indexů. Při opětovné sestavení indexů, se ujistěte, že přidělit dostatek paměti pro relaci, která znovu sestaví indexu.  K tomuto účelu zvýšit Třída prostředků uživatele, který má oprávnění k znovu sestavte index v této tabulce na doporučené minimum. Třída prostředků uživatele vlastníka databáze nelze změnit, pokud jste nevytvořili uživatele v systému, budete muset udělat nejdřív. Třída minimální doporučené prostředků je xlargerc Pokud používáte DW300 nebo méně largerc, pokud používáte DW400 DW600 a mediumrc, pokud používáte DW1000 a vyšší.

Dole je příklad postup přidělení více paměti na uživatele zvýšením jejich třída prostředků. Pro práci s třídy prostředků, najdete v části [třídy prostředků pro úlohy správy](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Krok 2: Znovu vytvořit Clusterované indexy columnstore s vyšší uživatelem prostředků – třída
Přihlaste se jako uživatel z kroku 1 (např. LoadUser), který je teď vyšší Třída prostředků, a spusťte příkazy ALTER INDEX. Ujistěte se, zda tento uživatel má oprávnění ALTER na tabulky, kde se index znovu sestaven. Tyto příklady ukazují, jak znovu sestavte index columnstore celý nebo opětovném sestavení jeden oddíl. V rozsáhlé tabulky je další praktická znovu sestavit indexy jeden oddíl v čase.

Místo znovu sestavit index, může případně zkopírujte tabulku, do nové tabulky [pomocí funkce CTAS](sql-data-warehouse-develop-ctas.md). Jakým způsobem je nejlepší? Pro velké objemy dat, je obvykle rychlejší než funkce CTAS [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). Příkaz ALTER INDEX pro menší objem dat, je jednodušší použít a nebude vyžadovat vyměnit v tabulce. V tématu **nové sestavení indexů se funkce CTAS a přepnutí oddílu** níže podrobnosti o tom, jak znovu vytvořit indexy s funkce CTAS.

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

Nové sestavení indexu v SQL Data Warehouse je offline operace.  Další informace o nové sestavení indexů, najdete v části ALTER INDEX REBUILD v [defragmentace indexy Columnstore](/sql/relational-databases/indexes/columnstore-indexes-defragmentation), a [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Krok 3: Ověření, že je vylepšený Clusterové columnstore segment kvality
Opětovné spuštění dotazu, které identifikovaných tabulku s nízká segmentovat kvality a ověření kvality segmentu je vylepšený.  Pokud ke zlepšení kvality segmentu, může to být, že jsou řádky v tabulce velmi široké.  Zvažte použití vyšší Třída prostředků nebo DWU při opětovném sestavování vaší indexy.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Nové sestavení indexů se funkce CTAS a přepnutí oddílu
Tento příklad používá [vytvořit tabulku AS vyberte funkce CTAS ()](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) prohlášení a přepnutí pro opětovné sestavení oddílů tabulky oddílu. 

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

Další podrobnosti o opětovné vytvoření oddílů pomocí funkce CTAS najdete v tématu [pomocí oddíly v SQL Data Warehouse](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Další postup
Další informace o vývoji tabulky najdete v tématu [vývoj tabulky](sql-data-warehouse-tables-overview.md).

