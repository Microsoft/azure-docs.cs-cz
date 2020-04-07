---
title: Indexování tabulek
description: Doporučení a příklady pro indexování tabulek v fondu SYNApse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0d63f2c29bfdbdf320185647bd33ec30500ed874
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742706"
---
# <a name="indexing-tables-in-synapse-sql-pool"></a>Indexování tabulek ve fondu Synapse SQL

Doporučení a příklady pro indexování tabulek v fondu SYNApse SQL.

## <a name="index-types"></a>Typy indexů

Fond SQL synapse nabízí několik možností indexování, včetně [clusterovaných indexů columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [seskupených indexů a neseskupených indexů](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)a možnosti neindexování označované také jako [haldy](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Chcete-li vytvořit tabulku s indexem, naleznete v dokumentaci [vytvořit tabulku (Synapse SQL pool).](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="clustered-columnstore-indexes"></a>Clusterované indexy columnstore

Ve výchozím nastavení synpse fond SQL vytvoří clusterovaný columnstore index, pokud nejsou v tabulce zadány žádné možnosti indexu. Clusterované tabulky columnstore nabízejí nejvyšší úroveň komprese dat a také nejlepší celkový výkon dotazu.  Clustered columnstore tabulky obecně předčí seskupený index nebo haldy tabulky a jsou obvykle nejlepší volbou pro velké tabulky.  Z těchto důvodů clusterované columnstore je nejlepší místo pro spuštění, když si nejste jisti, jak indexovat tabulku.  

Chcete-li vytvořit clustered columnstore tabulka, jednoduše zadejte CLUSTERED COLUMNSTORE INDEX v with klauzule nebo ponechat with klauzule off:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Existuje několik scénářů, kde clusterované columnstore nemusí být dobrou volbou:

- Columnstore tabulky nepodporují varchar(max), nvarchar(max) a varbinary(max). Zvažte haldy nebo seskupený index místo.
- Columnstore tabulky může být méně efektivní pro přechodná data. Zvažte haldy a možná i dočasné tabulky.
- Malé stoly s méně než 60 miliony řádků. Zvažte haldy tabulek.

## <a name="heap-tables"></a>Tabulky hald

Při dočasném přistání dat v fondu SYNAPse SQL, můžete zjistit, že pomocí haldy tabulky je celkový proces rychlejší. Důvodem je, že zatížení do hromady jsou rychlejší než index tabulky a v některých případech následné čtení lze provést z mezipaměti.  Pokud načítáte data pouze k fázi před spuštěním více transformací, načítání tabulky haldy tabulka je mnohem rychlejší než načítání dat do clusterované columnstore tabulky. Kromě toho načítání dat do [dočasné tabulky](sql-data-warehouse-tables-temporary.md) načte rychleji než načítání tabulky do trvalého úložiště.  

Pro malé vyhledávací tabulky, méně než 60 milionů řádků, často haldy tabulky smysl.  Cluster columnstore tabulky začnou dosáhnout optimální komprese, jakmile je více než 60 milionů řádků.

Chcete-li vytvořit tabulku haldy, jednoduše zadejte haldu v klauzuli WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Seskupené a neseskupené indexy

Seskupené indexy mohou překonat clusterované tabulky columnstore, když je třeba rychle načíst jeden řádek. U dotazů, u kterých je vyžadováno vyhledávání jednoho nebo velmi málo řádků s extrémní rychlostí, zvažte index clusteru nebo sekundární index bez clusterů. Nevýhodou použití clusterovaného indexu je, že pouze dotazy, které mají prospěch, jsou ty, které používají vysoce selektivní filtr ve sloupci clusterovaného indexu. Chcete-li zlepšit filtr na jiných sloupcích neseskupený index lze přidat do jiných sloupců. Každý index, který je přidán do tabulky však přidá místo a čas zpracování načíst.

Chcete-li vytvořit tabulku seskupených indexů, jednoduše zadejte clusterovaný index v klauzuli WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Chcete-li do tabulky přidat index bez seskupení, použijte následující syntaxi:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimalizace clusterovaných indexů columnstore

Clustered columnstore tabulky jsou uspořádány v datech do segmentů.  Vysoká kvalita segmentu je rozhodující pro dosažení optimálního výkonu dotazu v tabulce columnstore.  Kvalitu segmentu lze měřit počtem řádků ve skupině komprimovaných řádků.  Kvalita segmentu je nejoptimálnější, kde jsou alespoň 100 kB řádků na komprimovované skupiny řádků a zvýšení výkonu jako počet řádků na řádek skupiny přístup 1,048,576 řádky, což je nejvíce řádků skupiny řádků může obsahovat.

Níže uvedené zobrazení lze vytvořit a použít v systému vypočítat průměrné řádky na skupinu řádků a identifikovat všechny suboptimální indexy clusteru columnstore.  Poslední sloupec v tomto zobrazení generuje příkaz SQL, který lze použít k opětovnému sestavení indexů.

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

Nyní, když jste vytvořili zobrazení, spusťte tento dotaz k identifikaci tabulek se skupinami řádků s řádky menšími než 100 tisíc. Samozřejmě, možná budete chtít zvýšit práh 100K, pokud hledáte optimálnější kvalitu segmentu.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Po spuštění dotazu se můžete začít dívat na data a analyzovat výsledky. Tato tabulka vysvětluje, co hledat v analýze skupiny řádků.

| Sloupec | Jak tyto údaje používat |
| --- | --- |
| [table_partition_count] |Pokud je tabulka rozdělena na oddíly, můžete očekávat, že vyšší počet skupin řádků Open. Každý oddíl v distribuci může teoreticky mít k sobě přidruženou skupinu otevřených řádků. Započítejte to do vaší analýzy. Malá tabulka, která byla rozdělena, by mohla být optimalizována odstraněním dělení úplně, protože by to zlepšilo kompresi. |
| [row_count_total] |Celkový počet řádků pro tabulku. Tuto hodnotu můžete například použít k výpočtu procenta řádků v komprimovaném stavu. |
| [row_count_per_distribution_MAX] |Pokud jsou všechny řádky rovnoměrně rozloženy, bude tato hodnota cílovým počtem řádků na distribuci. Porovnejte tuto hodnotu s compressed_rowgroup_count. |
| To COMPRESSED_rowgroup_rows je v pořádku. |Celkový počet řádků ve formátu columnstore pro tabulku. |
| To COMPRESSED_rowgroup_rows_AVG je v pořádku. |Pokud je průměrný počet řádků výrazně menší než maximální počet řádků pro skupinu řádků, zvažte použití CTAS nebo ALTER INDEX REBUILD k opětovné muzici dat. |
| [COMPRESSED_rowgroup_count] |Počet skupin řádků ve formátu columnstore. Pokud je toto číslo velmi vysoké vzhledem k tabulce, je indikátorem, že hustota columnstore je nízká. |
| To COMPRESSED_rowgroup_rows_DELETED je v pořádku. |Řádky jsou logicky odstraněny ve formátu columnstore. Pokud je číslo vzhledem k velikosti tabulky vysoké, zvažte opětovné vytvoření oddílu nebo opětovné sestavení indexu, protože je fyzicky odeberete. |
| [COMPRESSED_rowgroup_rows_MIN] |Použijte to ve spojení se sloupci AVG a MAX k pochopení rozsahu hodnot pro skupiny řádků ve vašem columnstore. Nízké číslo nad prahovou hodnotou zatížení (102 400 na rozdělení zarovnané do oddílu) naznačuje, že optimalizace jsou k dispozici v zatížení dat |
| To COMPRESSED_rowgroup_rows_MAX je v pořádku. |Jak je uvedeno výše |
| [OPEN_rowgroup_count] |Otevřené skupiny řádků jsou normální. Dalo by se rozumně očekávat jednu skupinu řádků OPEN na distribuci tabulky (60). Nadměrná čísla naznačují načítání dat napříč oddíly. Zkontrolujte strategii dělení, abyste se ujistili, že je správná |
| [OPEN_rowgroup_rows] |Každá skupina řádků může mít maximálně 1 048 576 řádků. Pomocí této hodnoty můžete zjistit, jak jsou aktuálně otevřené skupiny řádků plné. |
| [OPEN_rowgroup_rows_MIN] |Otevřené skupiny označují, že data jsou buď načítána do tabulky, nebo že předchozí zatížení se přelije přes zbývající řádky do této skupiny řádků. Pomocí sloupců MIN, MAX, AVG můžete zjistit, kolik dat je v otevřených skupinách řádků. U malých tabulek by to mohlo být 100% všech dat! V takovém případě ALTER INDEX REBUILD vynutit data columnstore. |
| To OPEN_rowgroup_rows_MAX je v pořádku. |Jak je uvedeno výše |
| To OPEN_rowgroup_rows_AVG je v pořádku. |Jak je uvedeno výše |
| [CLOSED_rowgroup_rows] |Podívejte se na řádky skupiny uzavřených řádků jako kontrolu příčetnosti. |
| [CLOSED_rowgroup_count] |Počet uzavřených skupin řádků by měl být nízký, pokud jsou vůbec vidět. Uzavřené skupiny řádků lze převést na komprimované skupiny řádků pomocí ALTER INDEXU ... Reorganizovat, příkaz. To se však obvykle nevyžaduje. Uzavřené skupiny jsou automaticky převedeny na skupiny řádků columnstore pomocí procesu "n-tice mover" na pozadí. |
| [CLOSED_rowgroup_rows_MIN] |Uzavřené skupiny řádků by měly mít velmi vysokou míru naplnění. Pokud je míra naplnění pro uzavřenou skupinu řádků nízká, je vyžadována další analýza columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Jak je uvedeno výše |
| [CLOSED_rowgroup_rows_AVG] |Jak je uvedeno výše |
| To Rebuild_Index_SQL je v pořádku. |SQL pro znovuvytvoření indexu columnstore pro tabulku |

## <a name="causes-of-poor-columnstore-index-quality"></a>Příčiny nekvalitních indexů columnstore

Pokud jste identifikovali tabulky se špatnou kvalitou segmentu, chcete identifikovat hlavní příčinu.  Níže jsou uvedeny některé další běžné příčiny špatné kvality segmentu:

1. Tlak paměti při indexbyl vytvořen
2. Velký objem operací DML
3. Malé operace nebo operace zatížení pramínkem
4. Příliš mnoho oddílů

Tyto faktory mohou způsobit columnstore index mít výrazně menší než optimální 1 milion řádků na skupinu řádků. Mohou také způsobit, že řádky přejít do skupiny delta řádků namísto komprimované skupiny řádků.

### <a name="memory-pressure-when-index-was-built"></a>Tlak paměti při indexbyl vytvořen

Počet řádků na komprimovoluji řádek skupiny přímo souvisí s šířkou řádku a množství paměti k dispozici pro zpracování skupiny řádků.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Proto je osvědčeným postupem poskytnout relaci, která je zápis do tabulky index columnstore přístup k co nejvíce paměti, jak je to možné.  Vzhledem k tomu, že je kompromis mezi pamětí a souběžnosti, pokyny pro správné přidělení paměti závisí na data v každém řádku tabulky, jednotky datového skladu přidělené systému a počet slotů souběžnosti můžete dát relace, která je zápis dat do tabulky.

### <a name="high-volume-of-dml-operations"></a>Velký objem operací DML

Velký objem operací DML, které aktualizují a odstraňují řádky, může do columnstore zavést neefektivitu. To platí zejména při změně většiny řádků ve skupině řádků.

- Odstranění řádku ze komprimované skupiny řádků pouze logicky označí řádek jako odstraněný. Řádek zůstane ve skupině komprimovaných řádků, dokud nebude oddíl nebo tabulka znovu sestavena.
- Vložení řádku přidá řádek do vnitřní tabulky rowstore s názvem skupina delta řádků. Vložený řádek není převeden na columnstore, dokud je skupina delta řádků plná a není označena jako uzavřená. Skupiny řádků jsou uzavřeny, jakmile dosáhnou maximální kapacity 1 048 576 řádků.
- Aktualizace řádku ve formátu columnstore je zpracována jako logický odstranit a potom vložit. Vložený řádek může být uložen v rozdílovém úložišti.

Dávková aktualizace a vložení operace, které překračují prahovou hodnotu hromadné 102 400 řádků na rozdělení zarovnané oddíl přejít přímo do formátu columnstore. Však za předpokladu, že rovnoměrné rozdělení, budete muset upravit více než 6,144 milionu řádků v jedné operaci k tomu dochází. Pokud počet řádků pro dané rozdělení zarovnané oddíl je menší než 102 400 pak řádky přejít do úložiště delta a zůstat tam, dokud byly vloženy nebo upraveny dostatečné řádky zavřít skupinu řádků nebo index byl znovu sestaven.

### <a name="small-or-trickle-load-operations"></a>Malé operace nebo operace zatížení pramínkem

Malé zatížení, které proudí do fondu Synapse SQL jsou také někdy známé jako pramínek zatížení. Obvykle představují téměř konstantní datový proud dat, která jsou systémem ingestována. Však jako tento datový proud je téměř souvislý objem řádků není příliš velký. Více často než ne data je výrazně pod prahovou hodnotou požadovanou pro přímé zatížení do formátu columnstore.

V těchto situacích je často lepší přistát data nejprve v úložišti objektů blob Azure a nechat je hromadit před načtením. Tato technika je často známá jako *mikrodávkování*.

### <a name="too-many-partitions"></a>Příliš mnoho oddílů

Další věc, kterou je třeba zvážit, je dopad dělení na clusterované tabulky columnstore.  Před rozdělením, Synapse SQL fond již rozděluje data do 60 databází.  Dělení dále rozděluje data.  Pokud oddíl data, pak zvažte, že **každý** oddíl potřebuje alespoň 1 milion řádků využívat index clusterované columnstore.  Pokud tabulku rozdělíte na 100 oddílů, bude tabulka potřebovat alespoň 6 miliard řádků, aby bylo nutné využívat clusterovaný index columnstore (60 distribucí *100 oddílů* 1 milion řádků). Pokud tabulka 100 oddílů nemá 6 miliard řádků, snižte počet oddílů nebo zvažte použití tabulky haldy.

Jakmile jsou tabulky načteny s některými daty, postupujte podle následujících kroků k identifikaci a znovuvytvoření tabulky s suboptimální clustered columnstore indexy.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Obnova indexů za účelem zlepšení kvality segmentu

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Krok 1: Identifikace nebo vytvoření uživatele, který používá správnou třídu prostředků

Jedním z rychlých způsobů, jak okamžitě zlepšit kvalitu segmentu, je znovusestavit index.  SQL vrácené výše uvedené zobrazení vrátí příkaz ALTER INDEX REBUILD, který lze použít k opětovnému sestavení indexů. Při opětovném sestavení indexy, ujistěte se, že přidělit dostatek paměti relace, která obnovuje index.  Chcete-li to provést, zvyšte třídu prostředků uživatele, který má oprávnění k opětovnému sestavení indexu v této tabulce na doporučené minimum.

Níže je uveden příklad, jak přidělit více paměti uživateli zvýšením jejich třídy prostředků. Chcete-li pracovat s třídami prostředků, přečtěte [si informace o třídách prostředků pro správu pracovního vytížení](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Krok 2: Znovu sestavit clusterované indexy columnstore s uživatelem s vyšší třídou prostředků

Přihlaste se jako uživatel z kroku 1 (např. loaduser), který nyní používá vyšší třídu prostředků, a spusťte příkazy ALTER INDEX. Ujistěte se, že tento uživatel má oprávnění ALTER k tabulkám, kde je index znovu sestaven. Tyto příklady ukazují, jak znovu sestavit celý index columnstore nebo jak znovu sestavit jeden oddíl. Na velkých tabulkách je praktičtější znovu sestavit indexy jeden oddíl najednou.

Případně místo opětovného sestavení indexu můžete zkopírovat tabulku do nové tabulky [pomocí CTAS](sql-data-warehouse-develop-ctas.md). Který způsob je nejlepší? U velkých objemů dat je CTAS obvykle rychlejší než [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). U menších objemů dat se ALTER INDEX snadněji používá a nevyžaduje výměnu tabulky.

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

Opětovné sestavení indexu ve fondu Synapse SQL je operace offline.  Další informace o opětovném sestavení indexů naleznete v části ALTER INDEX REBUILD v části [Defragmentace indexů columnstore indexů](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)a [INDEX ALTER](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Krok 3: Ověření zlepšení kvality segmentu segmentu clusterovaného columnstore

Znovu spusťte dotaz, který identifikoval tabulku se špatnou kvalitou segmentu, a ověřte, zda se zlepšila kvalita segmentu.  Pokud se kvalita segmentu nezlepšila, může se podaným řádky v tabulce být mimořádně široké.  Zvažte použití vyšší třídy prostředků nebo DWU při opětovném sestavení indexů.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Opětovné sestavení indexů pomocí CTAS a přepínání oddílů

Tento příklad používá [příkaz CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a přepnutí oddílu k opětovnému sestavení oddílu tabulky.

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

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

Další podrobnosti o opětovném vytváření oddílů pomocí CTAS naleznete [v tématu Použití oddílů ve fondu SYNAPse SQL](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Další kroky

Další informace o vývoji tabulek naleznete v [tématu Vývoj tabulek](sql-data-warehouse-tables-overview.md).
