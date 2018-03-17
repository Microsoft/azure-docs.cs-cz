---
title: "Indexování tabulek v SQL Data Warehouse | Microsoft Azure"
description: "Začínáme s tabulkou indexování v Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 03/15/2018
ms.author: barbkess
ms.openlocfilehash: f0e19be5ec7453a76ff4f22ead6d3885e50beca8
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexování tabulek v SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Přehled][Overview]
> * [Datové typy][Data Types]
> * [Distribuce][Distribute]
> * [Index][Index]
> * [Oddíl][Partition]
> * [Statistiky][Statistics]
> * [Dočasné][Temporary]
> 
> 

SQL Data Warehouse nabízí několik možností indexování včetně [Clusterované indexy columnstore][clustered columnstore indexes], [Clusterované indexy a neclusterované indexy] [ clustered indexes and nonclustered indexes].  Kromě toho nabízí také žádná možnost indexu také označované jako [haldy][heap].  Tento článek popisuje výhody každý typ indexu, jakož i tipy pro získávání většina výkonu mimo vaší indexy. V tématu [vytvoření tabulky syntax] [ create table syntax] další podrobnosti o tom, jak vytvořit tabulku v SQL Data Warehouse.

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

* Tabulky Columnstore nepodporují, varchar(max), nvarchar(max) a varbinary(max).  Místo toho zvažte haldy nebo clusterovaný index.
* Tabulky Columnstore může být méně efektivní pro přechodný data.  Zvažte haldy a možná i dočasných tabulek.
* Malé tabulky s méně než 100 milionu řádků.  Vezměte v úvahu haldy tabulky.

## <a name="heap-tables"></a>Tabulky haldy
Když dočasně umisťujete data ve službě SQL Data Warehouse, možná zjistíte, že použitím tabulky haldy se celý proces zrychlí.  To je proto zatížení do hald je rychlejší než indexu tabulky a v některých případech lze provést další čtení z mezipaměti.  Pokud nahráváte data pouze za účelem jejich přípravy před spuštěním dalších transformací, nahrání tabulky do tabulky haldy bude mnohem rychlejší, než nahrání dat do clusterované tabulky columnstore. Kromě toho načítání dat do [dočasné tabulky] [ Temporary] také načte mnohem rychleji než načítání tabulky trvalém úložišti.  

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
Clusterované indexy může překonat Clusterované tabulky columnstore, když jeden řádek musí být rychle načíst.  Pro dotazy, kde je potřeba výkonu s extrémně rychlost jednu, nebo jen několik řádek vyhledávání zvažte index clusteru nebo sekundární neclusterovaný index.  Nevýhodou pomocí clusterovaného indexu je, že bude mít prospěch pouze na dotazy, které používají vysoce selektivní filtr pro sloupec clusterovaný index.  Pro zlepšení filtru na ostatních sloupců neclusterovaný index lze přidat na jiné sloupce.  Každý index, která se přidá do tabulky bude ale přidat místa a doba zpracování pro zatížení.

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
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimalizace Clusterované indexy columnstore
Clusterované tabulky columnstore jsou uspořádány v datech do segmentů.  S vysokou segment kvality je velmi důležité k dosažení výkonu optimální dotazu na tabulky columnstore.  Počet řádků ve skupině komprimované řádek lze měřit kvalitu segmentu.  Segment kvality je optimální, kde existují nejméně 100 tisíc řádků na jeden řádek komprimované skupiny a získat výkonu jako počet řádků na jeden řádek koncepci skupin 1 048 576 řádků, což je většina řádky, které může obsahovat skupiny řádků.

Níže zobrazení můžete vytvořit a použít v systému k výpočtu průměrné řádků na jeden řádek, skupiny a identifikovat žádné indexy columnstore neoptimálním průběhem clusteru.  Poslední sloupec v tomto zobrazení se vygeneruje jako příkaz jazyka SQL, který můžete použít k opětovnému sestavení indexů.

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

Teď, když jste vytvořili zobrazení, spusťte tento dotaz k identifikaci tabulek se skupiny řádků s méně než 100 tisíc řádků.  Samozřejmě můžete zvýšit prahovou hodnotu 100 tisíc Pokud hledáte další kvality optimální segmentu. 

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
Pokud jste našli tabulky s kvalitou nízký segmentu, můžete určit hlavní příčinu.  Tady jsou některé běžné příčiny nízký segment kvality:

1. Pokud byl vytvořený index nárokům na paměť
2. Velkému počtu operace DML
3. Malá nebo skapat zatížení operací
4. Příliš mnoho oddíly

Tyto faktory mohou způsobit index columnstore tak, aby měl výrazně menší než optimální 1 milionu řádků na skupinu řádků.  Také způsobit řádky, které chcete přejít na řádek skupinu rozdílů místo skupiny komprimované řádek. 

### <a name="memory-pressure-when-index-was-built"></a>Pokud byl vytvořený index nárokům na paměť
Počet řádků na skupinu komprimované řádek přímo souvisí s šířka řádku a množství paměti k dispozici pro zpracování skupiny řádků.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Osvědčeným postupem je proto umožnit relace, který je zápis do indexu columnstore tabulky přístup k tolik paměti.  Vzhledem k tomu, že je kompromis mezi paměti a souběžnost, pokyny k přidělení správné paměti závisí na datech v každý řádek tabulky, jednotky datového skladu přidělené systému a počet souběžnosti sloty, které poskytnete relace, který zapisuje data do tabulky.  Jako osvědčený postup doporučujeme začít s xlargerc, pokud používáte DW300 nebo méně largerc, pokud používáte DW400 DW600 a mediumrc, pokud používáte DW1000 a vyšší.

### <a name="high-volume-of-dml-operations"></a>Velkému počtu operace DML
K velkému počtu operace DML, které aktualizace a odstraňování řádků můžou představovat neefektivnost na columnstore. To platí hlavně při změně většina řádky v skupiny řádků.

* Odstranění řádku ze skupiny komprimované řádek pouze logicky označí řádek, jako je odstranit. Řádek zůstane ve skupině komprimované řádek, dokud znovu sestavit oddílu nebo tabulky.
* Vložíte řádek se přidá řádek, abyste na interní vytvořit tabulku nazývá skupina řádek delta. Vloženého řádku není převést na columnstore, dokud se skupiny řádků rozdílů je plný a je označené jako zavřené. Skupiny řádků jsou uzavřeny po uplynutí maximální kapacita 1 048 576 řádků. 
* Aktualizace řádků ve formátu columnstore zpracovávány jako logické odstranit a pak vložení. Vloženého řádku může být uložen v úložišti delta.

Zpracovat v dávce aktualizace a vkládání operace, které překročí prahovou hodnotu hromadné činí 102 400 řádků na jeden oddíl zarovnaný distribuční budou zapisovat přímo do formátu columnstore. Ale za předpokladu, že i distribuce, museli byste být úprava více než 6.144 milionu řádků v rámci jedné operace pro tuto funkci používat. Pokud počet řádků v daném oddílu zarovnán distribuce je menší než 102,400 pak řádky přejde k úložišti delta bude zůstat a dokud byl vložen nebo upravit tak, aby zavřete skupiny řádků dostatečná řádků nebo byla znovu sestavena index.

### <a name="small-or-trickle-load-operations"></a>Malá nebo skapat zatížení operací
Malá načte, že tok do SQL Data Warehouse se také někdy označuje jako skapat zatížení. Obvykle představují near nepřetržitý datový proud dat probíhá požita systému. Však jako tento datový proud je téměř průběžné svazku řádků není zvlášť velký. Častěji data je výrazně pod prahovou hodnotou. vyžaduje se pro přímé zatížení do formátu columnstore.

V těchto situacích je často lepší nejprve zobrazovat data v Azure blob storage a nechat ji hromadit před načtením. Tento postup se často označuje jako *dávkování micro*.

### <a name="too-many-partitions"></a>Příliš mnoho oddíly
Jiné věc vzít v úvahu, je vliv na vaše Clusterované tabulky columnstore oddíly.  Před oddílů, SQL Data Warehouse již rozděluje data do 60 databáze.  Vytváření oddílů vydělí další data.  Pokud oddílu dat, pak budete chtít názoru, že **každý** oddíl bude potřebovat tak, aby měl alespoň 1 milionu řádků, abyste mohli využívat výhod clusterovaný index columnstore.  Pokud oddílu tabulku do 100 oddílů, pak tabulka bude muset mít alespoň 6 miliardy řádků, abyste mohli využívat výhod clusterovaný index columnstore (60 distribuce * 100 oddíly * 1 milionu řádků). Pokud 100 oddílu tabulky nemá 6 miliardy řádků, snižte počet oddílů nebo zvažte použití haldy tabulku místo.

Jakmile vaše tabulky byly načteny s některá data, níže uvedených pokynů k identifikaci a znovu vytvořit tabulky s indexy columnstore neoptimálním průběhem clusteru.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Nové sestavení indexů ke zlepšení kvality segmentu
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Krok 1: Určení nebo vytvoření uživatele, který používá třída správné prostředků
Rychlým způsobem okamžitě zlepšení kvality segmentu je znovu sestavte index.  Příkaz ALTER INDEX REBUILD, který můžete použít k opětovnému sestavení indexů vrátí SQL vrácený výše uvedené zobrazení.  Při opětovné sestavení indexů, se ujistěte, že přidělit dostatek paměti pro relace, který bude opětovné sestavení indexu.  K tomuto účelu zvýšit Třída prostředků uživatele, který má oprávnění k znovu sestavte index v této tabulce na doporučené minimum.  Třída prostředků uživatele vlastníka databáze nelze změnit, takže pokud jste dosud nevytvořili uživatele v systému, bude potřeba k tomu nejdřív.  Minimální, doporučujeme je xlargerc Pokud používáte DW300 nebo méně largerc, pokud používáte DW400 DW600 a mediumrc, pokud používáte DW1000 a vyšší.

Dole je příklad postup přidělení více paměti na uživatele zvýšením jejich třída prostředků.  Další informace o prostředku třídy a postup vytvoření nového uživatele najdete v [souběžnosti a úlohy správy] [ Concurrency] článku.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Krok 2: Znovu vytvořit Clusterované indexy columnstore s vyšší uživatelem prostředků – třída
Přihlášení jako uživatel z kroku 1 (např. LoadUser), které teď používá vyšší Třída prostředků a spusťte příkazy ALTER INDEX.  Ujistěte se, zda tento uživatel má oprávnění ALTER na tabulky, kde se index znovu sestaven.  Tyto příklady ukazují, jak znovu sestavte index columnstore celý nebo opětovném sestavení jeden oddíl. V rozsáhlé tabulky je další praktická znovu sestavit indexy jeden oddíl v čase.

Místo znovu sestavit index, může případně zkopírujte tabulku, do nové tabulky pomocí [funkce CTAS][CTAS].  Jakým způsobem je nejlepší? Pro velké objemy dat [funkce CTAS] [ CTAS] je obvykle rychlejší než [ALTER INDEX][ALTER INDEX]. Pro menší objem dat [ALTER INDEX] [ ALTER INDEX] je jednodušší použít a nebude vyžadovat vyměnit v tabulce.  V tématu **nové sestavení indexů se funkce CTAS a přepnutí oddílu** níže podrobnosti o tom, jak znovu vytvořit indexy s funkce CTAS.

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

Nové sestavení indexu v SQL Data Warehouse je offline operace.  Další informace o nové sestavení indexů, najdete v části ALTER INDEX REBUILD v [defragmentace indexy Columnstore][Columnstore Indexes Defragmentation], a [ALTER INDEX] [ ALTER INDEX].

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Krok 3: Ověření, že je vylepšený Clusterové columnstore segment kvality
Opětovné spuštění dotazu, které identifikovaných tabulku s nízká segmentovat kvality a ověření kvality segmentu je vylepšený.  Pokud ke zlepšení kvality segmentu, může to být, že jsou řádky v tabulce velmi široké.  Zvažte použití vyšší Třída prostředků nebo DWU při opětovném sestavování vaší indexy.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Nové sestavení indexů se funkce CTAS a přepnutí oddílu
Tento příklad používá [funkce CTAS] [ CTAS] a přepnutí pro opětovné sestavení oddílů tabulky oddílu. 

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

Další podrobnosti o opětovné vytvoření oddíly používající `CTAS`, najdete v článku [oddílu] [ Partition] článku.

## <a name="next-steps"></a>Další postup
Další informace najdete v článcích na [tabulky přehled][Overview], [tabulky datové typy][Data Types], [distribuci tabulku] [ Distribute], [Vytváření oddílů tabulky][Partition], [zachování statistiky tabulky] [ Statistics] a [Dočasných tabulek][Temporary].  Další informace o osvědčených postupech najdete v tématu [SQL Data Warehouse osvědčené postupy][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./resource-classes-for-workload-management.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx
[heap]: https://msdn.microsoft.com/library/hh213609.aspx
[clustered indexes and nonclustered indexes]: https://msdn.microsoft.com/library/ms190457.aspx
[create table syntax]: https://msdn.microsoft.com/library/mt203953.aspx
[Columnstore Indexes Defragmentation]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[clustered columnstore indexes]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
