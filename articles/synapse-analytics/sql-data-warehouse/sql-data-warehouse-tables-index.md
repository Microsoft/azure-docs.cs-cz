---
title: Indexování tabulek
description: Doporučení a příklady pro indexování tabulek ve vyhrazeném fondu SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: fabbdf330d43737ffa85379f9cc4d5ac59c4a734
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673514"
---
# <a name="indexing-dedicated-sql-pool-tables-in-azure-synapse-analytics"></a>Indexování vyhrazených tabulek fondu SQL ve službě Azure synapse Analytics

Doporučení a příklady pro indexování tabulek ve vyhrazeném fondu SQL

## <a name="index-types"></a>Typy indexů

Vyhrazený fond SQL nabízí několik možností indexování včetně [clusterovaných indexů columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [clusterovaných indexů a neclusterovaných indexů](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a možnost bez indexu, která se označuje také jako [halda](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  

Chcete-li vytvořit tabulku s indexem, přečtěte si dokumentaci [Create Table (vyhrazený fond SQL)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="clustered-columnstore-indexes"></a>Clusterované indexy columnstore

Ve výchozím nastavení vytvoří vyhrazený fond SQL clusterovaný index columnstore, pokud v tabulce nejsou zadány žádné možnosti indexu. Clusterované tabulky columnstore nabízejí nejvyšší úroveň komprese dat i nejlepší celkový výkon dotazů.  Clusterované tabulky columnstore budou všeobecně překoná clusterovaných indexů nebo tabulek haldy a jsou obvykle nejlepší volbou pro velké tabulky.  Z těchto důvodů je clusterový columnstore nejlepší místo, kde si nejste jisti, jak indexovat tabulku.  

Pokud chcete vytvořit clusterovanou tabulku columnstore, stačí v klauzuli WITH zadat CLUSTEROVANÝ INDEX COLUMNSTORE, nebo ponechte klauzuli WITH vypnuto:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Existuje několik scénářů, kdy clusterový columnstore nemůže být dobrou možností:

- Tabulky columnstore nepodporují varchar (max), nvarchar (max) a varbinary (max). Místo toho zvažte haldu nebo clusterovaný index.
- Tabulky columnstore můžou být pro přechodná data méně efektivní. Zvažte haldu a případně i dočasné tabulky.
- Malé tabulky s méně než 60 000 000 řádky. Zvažte tabulky haldy.

## <a name="heap-tables"></a>Tabulky haldy

Když dočasně vydáváte data ve vyhrazeném fondu SQL, může se stát, že při použití tabulky haldy bude celý proces rychlejší. Důvodem je to, že zatížení haldami je rychlejší než indexování tabulek a v některých případech je možné provést následné čtení z mezipaměti.  Pokud načítáte data pouze do fáze před spuštěním více transformací, načítání tabulky do tabulky haldy je mnohem rychlejší než načítání dat do clusterované tabulky columnstore. Kromě toho se načítání dat do [dočasné tabulky](sql-data-warehouse-tables-temporary.md) načítá rychleji než načítání tabulky do trvalého úložiště.  Po načtení dat můžete v tabulce vytvořit indexy pro rychlejší výkon dotazů.  

Pokud je více než 60 000 000 řádků, začne docházet k optimální kompresi v tabulkách clusteru columnstore.  V případě malých vyhledávacích tabulek, méně než 60 000 000 řádků, zvažte použití HALDy nebo clusterovaného indexu pro rychlejší dotazování na výkon. 

Chcete-li vytvořit tabulku haldy, stačí zadat HALDu v klauzuli WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Clustery a neseskupené indexy

Clusterované indexy můžou překoná clusterovaných tabulek columnstore, když je potřeba rychle načíst jeden řádek. U dotazů, u kterých je potřeba, aby se při extrémní rychlosti vybralo jedno nebo hodně málo vyhledávaných řádků, vezměte v úvahu index clusteru nebo neclusterovaný sekundární index. Nevýhodou použití clusterovaného indexu je to, že pouze dotazy, které mají výhodu, jsou ty, které využívají vysoce selektivní filtr u sloupce clusterovaného indexu. Aby bylo možné vylepšit filtr na ostatních sloupcích, můžete do jiných sloupců přidat neclusterovaný index. Každý index, který je přidán do tabulky, však přidává prostor a dobu zpracování pro načtení.

Chcete-li vytvořit tabulku clusterovaných indexů, stačí zadat CLUSTEROVANÝ INDEX v klauzuli WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Chcete-li do tabulky přidat neclusterovaný index, použijte následující syntaxi:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimalizace clusterovaných indexů columnstore

Clusterované tabulky columnstore jsou v datech uspořádány do segmentů.  Pro zajištění optimálního výkonu dotazů v tabulce columnstore je klíčové vysoké kvality segmentů.  Kvalita segmentů se dá měřit počtem řádků v komprimované skupině řádků.  Kvalita segmentů je optimální, pokud existuje alespoň 100 tisíc řádků na komprimovanou skupinu řádků a získá výkon jako počet řádků na skupinu řádků s přístupem 1 048 576 řádků, což je většina řádků, které může skupina řádků obsahovat.

Níže uvedené zobrazení je možné vytvořit a použít ve vašem systému k výpočtu průměrných řádků na skupinu řádků a určení všech dílčích indexů columnstore clusteru.  Poslední sloupec v tomto zobrazení vygeneruje příkaz SQL, který lze použít k opětovnému sestavení indexů.

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

Teď, když jste vytvořili zobrazení, spusťte tento dotaz, abyste identifikovali tabulky se skupinami řádků s méně než 100 tisíc řádky. Je samozřejmě možné, že budete chtít zvýšit prahovou hodnotu 100 tisíc, pokud hledáte vyšší optimální kvalitu segmentů.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Jakmile spustíte dotaz, můžete začít se podívat na data a analyzovat výsledky. V této tabulce je vysvětleno, co se má v analýze skupiny řádků Hledat.

| Sloupec | Jak používat tato data |
| --- | --- |
| [table_partition_count] |Pokud je tabulka rozdělená na oddíly, může se stát, že budete chtít zobrazit větší počet otevřených skupin řádků. Každý oddíl v distribuci se může teoreticky používat k přidružení otevřené skupiny řádků. Přiložte to do vaší analýzy. Malá tabulka, která byla rozdělená na oddíly, by mohla být optimalizovaná tak, že zcela odebere oddíly, protože by to mohlo zlepšit komprimaci. |
| [row_count_total] |Celkový počet řádků tabulky Pomocí této hodnoty můžete například vypočítat procento řádků v komprimovaném stavu. |
| [row_count_per_distribution_MAX] |Pokud jsou všechny řádky rovnoměrně distribuované, tato hodnota by představovala cílový počet řádků na distribuci. Porovnejte tuto hodnotu s compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Celkový počet řádků ve formátu columnstore pro tabulku |
| [COMPRESSED_rowgroup_rows_AVG] |Pokud je průměrný počet řádků výrazně menší než maximální počet řádků pro skupinu řádků, zvažte použití CTAS nebo ALTER INDEX rebuild pro komprimaci dat. |
| [COMPRESSED_rowgroup_count] |Počet skupin řádků ve formátu columnstore. Pokud je toto číslo velmi vysoké ve vztahu k tabulce, jedná se o indikátor, že hustota columnstore je nízká. |
| [COMPRESSED_rowgroup_rows_DELETED] |Řádky jsou logicky smazány ve formátu columnstore. Pokud je číslo vysoké vzhledem k velikosti tabulky, zvažte možnost znovu vytvořit oddíl nebo index znovu sestavit, protože je fyzicky odebere. |
| [COMPRESSED_rowgroup_rows_MIN] |Tuto kombinaci použijte spolu se sloupci průměr a maximum, abyste porozuměli rozsahu hodnot pro skupiny řádků v columnstore. Nízké číslo nad prahovou hodnotou zatížení (102 400 na distribuci zarovnané na oddíl) naznačuje, že v načtení dat jsou k dispozici optimalizace. |
| [COMPRESSED_rowgroup_rows_MAX] |Jak je uvedeno výše |
| [OPEN_rowgroup_count] |Otevřené skupiny řádků jsou normální. Jeden z nich by měl přiměřeně očekávat jednu skupinu otevřených řádků na distribuci tabulky (60). Příliš velké počty navrhují načítání dat napříč oddíly. Zkontrolujte strategii dělení, abyste se ujistili, že se jedná o zvuk. |
| [OPEN_rowgroup_rows] |Každá skupina řádků může mít 1 048 576 řádků jako maximum. Pomocí této hodnoty můžete zjistit, jak plné jsou aktuálně otevřené skupiny řádků. |
| [OPEN_rowgroup_rows_MIN] |Otevřené skupiny označují, že data jsou buď trickle načtena do tabulky, nebo že předchozí zatížení se z zbývajících řádků do této skupiny řádků zavedlo. Sloupce MIN, MAX, AVG použijte, pokud chcete zjistit, kolik dat je v otevřených skupinách řádků nastaveno na hodnotu SAT. V malých tabulkách může být 100% všech dat! V takovém případě změňte INDEX opětovného sestavení za účelem vynucení dat do columnstore. |
| [OPEN_rowgroup_rows_MAX] |Jak je uvedeno výše |
| [OPEN_rowgroup_rows_AVG] |Jak je uvedeno výše |
| [CLOSED_rowgroup_rows] |Prohlédněte si řádky skupiny uzavřených řádků jako správnosti kontrolu. |
| [CLOSED_rowgroup_count] |Počet uzavřených skupin řádků by měl být nízký, pokud jsou všechny zobrazeny vůbec. Skupiny uzavřených řádků lze převést na komprimované skupiny řádků pomocí příkazu ALTER INDEX... Reorganizovat příkaz. To se ale obvykle nevyžaduje. Uzavřené skupiny se automaticky převedou na skupiny řádků columnstore na pozadí "zpracování řazené kolekce členů". |
| [CLOSED_rowgroup_rows_MIN] |Skupiny uzavřených řádků by měly mít velmi vysokou míru výplně. Pokud je pro skupinu uzavřených řádků Nízká sazba, je potřeba další analýza columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Jak je uvedeno výše |
| [CLOSED_rowgroup_rows_AVG] |Jak je uvedeno výše |
| [Rebuild_Index_SQL] |SQL pro opětovné sestavení indexu columnstore pro tabulku |

## <a name="causes-of-poor-columnstore-index-quality"></a>Příčiny nekvalitních indexů columnstore

Pokud jste identifikovali tabulky s nízkou kvalitou segmentů, chcete identifikovat hlavní příčinu.  Níže jsou uvedeny některé další běžné příčiny špatné kvality segmentu:

1. Tlak paměti při sestavení indexu
2. Velký objem operací DML
3. Malé nebo trickle operace načítání
4. Příliš mnoho oddílů

Tyto faktory můžou způsobit, že index columnstore bude mnohem menší než optimální 1 000 000 řádků na skupinu řádků. Mohou také způsobit, že řádky budou přejít do skupiny řádků Delta místo do komprimované skupiny řádků.

### <a name="memory-pressure-when-index-was-built"></a>Tlak paměti při sestavení indexu

Počet řádků na komprimovanou skupinu řádků přímo souvisí se šířkou řádku a velikostí paměti, která je k dispozici pro zpracování skupiny řádků.  Když se řádky zapisují do tabulek columnstore při zatížení paměti, může tím utrpět kvalita segmentů columnstore.  Proto je osvědčeným postupem poskytnout relaci, která zapisuje do tabulek indexu columnstore, přístup k co nejvíce paměti.  Vzhledem k tomu, že existuje kompromis mezi pamětí a souběžnou, jsou pokyny týkající se správného přidělení paměti závislé na datech v jednotlivých řádcích tabulky, jednotkách datového skladu, které jsou přiděleny vašemu systému, a počtu slotů souběžnosti, které můžete přiřadit k relaci, která zapisuje data do tabulky.

### <a name="high-volume-of-dml-operations"></a>Velký objem operací DML

Velký objem operací DML, které aktualizují a odstraňují řádky, mohou způsobit neúčinnost do columnstore. To platí hlavně v případě, že většina řádků ve skupině řádků je upravena.

- Odstranění řádku ze zkomprimované skupiny řádků pouze logicky označí řádek jako odstraněný. Řádek zůstane ve zkomprimované skupině řádků, dokud není oddíl nebo tabulka znovu sestavena.
- Vložení řádku přidá řádek do interní tabulky rowstore s názvem rozdílové skupiny řádků. Vložená řádka není převedena do columnstore, dokud není skupina rozdílových řádků zaplněna a je označena jako uzavřená. Skupiny řádků se zavřou, jakmile dosáhnou maximální kapacity 1 048 576 řádků.
- Aktualizace řádku ve formátu columnstore je zpracována jako logické odstranění a potom vložení. Vložený řádek může být uložený v rozdílovém úložišti.

Dávkové operace aktualizace a vložení, které přesahují hromadnou prahovou hodnotu 102 400 řádků na distribuci zarovnaným na oddíl, přejdou přímo do formátu columnstore. Za předpokladu rovnoměrného rozdělení byste ale museli upravovat více než 6 144 000 řádků v rámci jedné operace, aby k tomu mohlo dojít. Pokud je počet řádků pro dané rozdělení zarovnaný na oddíly menší než 102 400, řádky se přejdou do rozdílového úložiště a zůstanou tam, dokud nebudou vloženy nebo upraveny dostatečné řádky, aby se zavřela skupina řádků nebo aby byl index znovu sestaven.

### <a name="small-or-trickle-load-operations"></a>Malé nebo trickle operace načítání

Malá zatížení, která se přenášejí do vyhrazeného fondu SQL, se také někdy označují jako trickle zatížení. Typicky představují skoro konstantní datový proud dat zpracovávaných systémem. Vzhledem k tomu, že je tento datový proud blízko nepřetržitě, není objem řádků obzvláště velký. Častěji než data jsou výrazně pod prahovou hodnotou nutnou pro přímé zatížení ve formátu columnstore.

V těchto situacích je často lepší nakládat data jako první v úložišti objektů BLOB v Azure a nechat si je shromáždit před načtením. Tato technika se často označuje jako *mikrodávkování*.

### <a name="too-many-partitions"></a>Příliš mnoho oddílů

Další věcí, kterou je potřeba vzít v úvahu, je dopad dělení na oddíly v clusterovaných tabulkách columnstore.  Vyhrazený fond SQL už před vytvořením oddílů již rozděluje vaše data do databází 60.  Dělení dále rozděluje vaše data.  Při vytváření oddílů dat je potřeba vzít v úvahu, že **každý** oddíl potřebuje aspoň 1 000 000 řádků, aby bylo možné využít clusterový index columnstore.  Pokud vytváříte oddíly tabulky na oddíly 100, je potřeba, aby tabulka měla aspoň 6 000 000 000 řádků, abyste využili výhod clusterovaného indexu columnstore (60 distribuce *100 oddíly* 1 000 000 řádků). Pokud tabulka 100-partition neobsahuje 6 000 000 000 řádků, snižte počet oddílů nebo zvažte použití tabulky haldy.

Po načtení tabulek s některými daty pomocí následujících kroků identifikujte a znovu sestavíte tabulky s dílčími optimálními clusterovanými indexy columnstore.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Nové sestavení indexů pro zlepšení kvality segmentu

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Krok 1: identifikace nebo vytvoření uživatele, který používá správnou třídu prostředků

Jedním z rychlých způsobů, jak okamžitě vylepšit kvalitu segmentů, je znovu sestavit index.  SQL vrácený výše uvedeným zobrazením vrátí příkaz ALTER INDEX Rebuild, který lze použít k opětovnému sestavení indexů. Při opětovném sestavování indexů se ujistěte, že přidělíte dostatek paměti relaci, která znovu sestaví index.  Provedete to tak, že zvýšíte třídu prostředků uživatele, který má oprávnění k opětovnému sestavení indexu v této tabulce do doporučeného minima.

Níže je uveden příklad, jak přidělit uživateli více paměti tím, že zvýšíte jejich třídu prostředků. Chcete-li pracovat s třídami prostředků, přečtěte si téma [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Krok 2: opětovné sestavení clusterovaných indexů columnstore s vyšším uživatelským třídou prostředků

Přihlaste se jako uživatel z kroku 1 (například LoadUser), který teď používá vyšší třídu prostředků, a spusťte příkazy ALTER INDEX. Ujistěte se, že tento uživatel má oprávnění ALTER pro tabulky, ve kterých je index znovu sestaven. Tyto příklady ukazují, jak znovu sestavit celý index columnstore nebo jak znovu sestavit jeden oddíl. Ve velkých tabulkách je praktické znovu sestavovat indexy v jednom oddílu.

Případně místo opakovaného sestavování indexu můžete tabulku zkopírovat do nové tabulky [pomocí CTAS](sql-data-warehouse-develop-ctas.md). Jaký je nejlepší způsob? Pro velké objemy dat je CTAS obvykle rychlejší než [Změna indexu](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Pro menší objemy dat je snazší použít příkaz ALTER INDEX a nevyžaduje, abyste tabulku vyměnili.

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

Nové sestavení indexu ve vyhrazeném fondu SQL je operace offline.  Další informace o opětovném sestavení indexů naleznete v části ALTER INDEX Rebuild v tématu [Defragmentace](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)indexů columnstore a v článku [ALTER index](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Krok 3: ověření kvality segmentu clusterovaného v clusteru

Znovu spusťte dotaz, který identifikoval tabulku s nízkou kvalitou segmentů, a ověřte, že kvalita segmentu je vylepšená.  Pokud se kvalita segmentu nezlepšila, může to být, že řádky v tabulce jsou velmi velké.  Zvažte použití vyšší třídy prostředků nebo DWU při opakovaném sestavování indexů.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Opakované sestavení indexů pomocí CTAS a přepínání oddílů

V tomto příkladu se používá příkaz [CREATE TABLE AS Select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a přepínání oddílů pro opětovné sestavení oddílu tabulky.

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

Další podrobnosti o opětovném vytvoření oddílů pomocí CTAS najdete v tématu [použití oddílů ve vyhrazeném fondu SQL](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Další kroky

Další informace o vývoji tabulek najdete v tématu [vývoj tabulek](sql-data-warehouse-tables-overview.md).
