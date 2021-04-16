---
title: Ladění výkonu s využitím uspořádaného clusterovaného indexu columnstore
description: Doporučení a pokyny, které byste měli znát při použití seřazeného clusterovaného indexu columnstore pro zlepšení výkonu dotazů ve vyhrazených fondech SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/13/2021
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3245f560d9a5afb1f9cf8824eeaa3bc681706794
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389668"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Ladění výkonu s využitím uspořádaného clusterovaného indexu columnstore  

Když uživatelé dotazují tabulku columnstore ve vyhrazeném fondu SQL, optimalizuje zkontroluje minimální a maximální hodnoty uložené v jednotlivých segmentech.  Segmenty mimo hranice predikátu dotazu se nečtou z disku do paměti.  Dotaz může dosáhnout rychlejšího výkonu, pokud je počet čtených segmentů a jejich celková velikost malá.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Seřazený a neuspořádaný clusterovaný index columnstore

Ve výchozím nastavení pro každou tabulku vytvořenou bez možnosti indexu vytvoří interní komponenta (Tvůrce indexů) neuspořádaný clusterovaný index columnstore (Ski).  Data v jednotlivých sloupcích jsou komprimována do samostatného segmentu skupiny řádků Ski.  V rozsahu hodnot každého segmentu jsou metadata, takže segmenty, které jsou mimo hranice predikátu dotazu, se při provádění dotazu nečtou z disku.  Ski nabízí nejvyšší úroveň komprese dat a snižuje velikost segmentů ke čtení, aby dotazy mohly běžet rychleji. Vzhledem k tomu, že tvůrce indexů neřadí data před jejich komprimací do segmentů, může dojít k segmentům s překrývajícími se rozsahy hodnot, což způsobilo, že dotazy budou číst více segmentů z disku a trvá déle.  

Při vytváření seřazené konzulární instrukce vyřadí vyhrazený modul SQL fondu stávající data v paměti pomocí klíčů, než je tvůrce indexů komprimuje do segmentů indexu.  U seřazených dat je segment překrývající se snížen, takže dotazy mají efektivnější odstraňování segmentů, což znamená rychlejší výkon, protože počet segmentů ke čtení z disku je menší.  Pokud se všechna data dají řadit v paměti najednou, můžete se vyhnout překrývání segmentu.  V důsledku velkých tabulek v datových skladech se tento scénář často neprovádí.  

Chcete-li kontrolovat rozsahy segmentů pro sloupec, spusťte následující příkaz s názvem tabulky a názvem sloupce:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> V seřazené tabulce Ski se v rámci této dávky seřadí nová data, která jsou výsledkem stejné dávky operací DML nebo načítání dat, ale neexistují žádná globální řazení napříč všemi daty v tabulce.  Uživatelé mohou znovu sestavit uspořádanou INSTRUKCi pro řazení všech dat v tabulce.  V vyhrazeném fondu SQL je opětovné sestavení indexu columnstore operací offline.  Pro dělenou tabulku je opětovné sestavení provedeno po jednom oddílu.  Data v oddílu, který se má znovu sestavit, jsou "offline" a nejsou k dispozici, dokud není znovu dokončeno opětovné sestavení pro tento oddíl. 

## <a name="query-performance"></a>Výkon dotazů

Nárůst výkonu dotazu od seřazené instrukce závisí na vzorech dotazů, velikosti dat, jak dobře jsou data seřazená, na fyzické struktuře segmentů a na třídě DWU a prostředku zvolené pro provedení dotazu.  Před výběrem sloupců řazení při návrhu seřazené tabulky s INSTRUKCEmi musí uživatelé zkontrolovat všechny tyto faktory.

Dotazy se všemi těmito vzory obvykle fungují rychleji s uspořádanou konzulární instrukcí.  
1. Dotazy mají predikáty rovnosti, nerovnosti nebo rozsahu.
1. Sloupce predikátu a seřazené sloupce s INSTRUKCEmi jsou stejné.  
1. Sloupce predikátu se používají ve stejném pořadí jako pořadí sloupců uspořádaných sloupců s konzulárními instrukcemi.  
 
V tomto příkladu má tabulka T1 clusterovaný index columnstore seřazený v sekvenci Col_C, Col_B a Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Výkon dotazů 1 může využít více než jedna z řazené Ski, než ostatní tři dotazy. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Výkon načítání dat

Výkon načítání dat do seřazené tabulky Ski je podobný tabulce děleno.  Načítání dat do seřazené tabulky s INSTRUKCEmi může v důsledku operace řazení dat trvat déle než neuspořádaná tabulka INSTRUKCí. dotazy ale můžou běžet rychleji, ale s uspořádanou konzulární rutinou.  

Zde je příklad porovnání výkonu načítání dat do tabulek s různými schématy.

![Pruhový graf, který zobrazuje porovnání výkonu při načítání dat do tabulek s různými schématy.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Tady je příklad porovnání výkonu dotazů mezi Ski a seřazenou konzulární instrukcí.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Zmenšení překrývajících se segmentů

Počet překrývajících se segmentů závisí na velikosti dat, která se mají seřadit, dostupné paměti a nastavení maximálního stupně paralelismu (MAXDOP) během vytváření řazené Ski. Níže jsou uvedeny možnosti, jak omezit segment překrývající se při vytváření uspořádané konzulární instrukce.

- Třídu prostředků xlargerc můžete použít na vyšší DWU, abyste umožnili více paměti pro řazení dat před tím, než tvůrce indexů komprimuje data do segmentů.  V segmentu indexu nemůže být fyzické umístění dat změněno.  Neexistuje žádné řazení dat v rámci segmentu nebo napříč segmenty.  

- Vytvořte uspořádanou INSTRUKCi s MAXDOP = 1.  Každé vlákno používané pro seřazené vytváření konzulárních instrukcí funguje na podmnožině dat a seřadí je místně.  Neexistuje žádné globální řazení napříč daty seřazenými podle různých vláken.  Použití paralelních vláken může zkrátit čas k vytvoření seřazené instrukce, ale vygeneruje více překrývajících se segmentů než použití jednoho vlákna.  V současné době se možnost MAXDOP podporuje jenom při vytváření seřazené tabulky INSTRUKCí pomocí CREATE TABLE jako příkazu SELECT.  Vytvoření seřazené instrukce prostřednictvím příkazu CREATE INDEX nebo CREATE TABLE nepodporuje možnost MAXDOP. Třeba

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Před jejich nařazením do tabulek předem Seřaďte data pomocí klíčů řazení.

Tady je příklad uspořádané distribuce tabulek Ski, která má nulový segment překrývající se nad doporučeními. Seřazená tabulka Ski je vytvořená v databázi DWU1000c prostřednictvím CTAS z tabulky haldy 20 GB s použitím MAXDOP 1 a xlargerc.  INSTRUKCE je seřazená na sloupec typu BIGINT bez duplicit.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Vytváření uspořádané konzulární instrukce pro velké tabulky

Vytvoření seřazené konzulární instrukce je offline operace.  Pro tabulky, které neobsahují oddíly, data nebudou k dispozici uživatelům, dokud se nedokončí proces vytváření řazené Ski.   Pro dělené tabulky, protože modul vytváří seřazený oddíl s pokyny podle oddílu, uživatelé budou mít stále přístup k datům v oddílech, kde se vytváření řazené konzulárních instrukcí nezpracovává.   Tuto možnost můžete použít k minimalizaci výpadku během uspořádaného vytváření Ski v rozsáhlých tabulkách: 

1.    Vytvořte oddíly pro cílovou rozsáhlou tabulku (s názvem Table_A).
2.    Vytvoří prázdnou seřazenou tabulku Ski (s názvem Table_B) se stejným schématem Table a partition jako tabulka a.
3.    Umožňuje přepnout jeden oddíl z tabulky A na tabulku B.
4.    Spusťte příkaz ALTER INDEX <Ordered_CCI_Index> na <Table_B> znovu sestavit oddíl = <Partition_ID> v tabulce B pro opětovné sestavení přepnutého oddílu.  
5.    Zopakujte kroky 3 a 4 pro každý oddíl v Table_A.
6.    Po přepnutí všech oddílů z Table_A na Table_B a jejich opětovné vytvoření, vyřazení Table_A a přejmenování Table_B na Table_A. 

>[!TIP]
> U vyhrazené tabulky fondu SQL s uspořádanou INSTRUKCí upraví opětovné sestavení indexu znovu seřadí data v databázi tempdb. Monitoruje databázi tempdb během operací opětovného sestavení. Pokud potřebujete více místa v databázi tempdb, naplánujte horizontální navýšení kapacity fondu. Po dokončení opětovného sestavení indexu se horizontální navýšení kapacity znovu zvětší.
>
> U vyhrazené tabulky fondu SQL s uspořádanou INSTRUKCí změna uspořádání indexu data znovu neřadí. Pokud chcete data použít, použijte příkaz ALTER INDEX Rebuild.

## <a name="examples"></a>Příklady

**Určitého. Chcete-li kontrolovat seřazené sloupce a pořadí pořadí:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Změna pořadí sloupců, přidání nebo odebrání sloupců ze seznamu objednávek nebo změna z instrukce na seřazenou INSTRUKCi:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).
