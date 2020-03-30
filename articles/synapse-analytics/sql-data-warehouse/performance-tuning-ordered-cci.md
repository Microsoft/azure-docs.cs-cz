---
title: Ladění výkonu s využitím uspořádaného clusterovaného indexu columnstore
description: Doporučení a důležité informace, které byste měli vědět, jak používáte objednané clusterované columnstore index ke zlepšení výkonu dotazu.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: a5bb048a2368f60a83e70dcd6d1ce663ce70a885
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350924"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Ladění výkonu s využitím uspořádaného clusterovaného indexu columnstore  

Když se uživatelé dotazují na tabulku columnstore v SQL Analytics, optimalizátor zkontroluje minimální a maximální hodnoty uložené v každém segmentu.  Segmenty, které jsou mimo hranice predikátu dotazu, se nečtou z disku do paměti.  Dotaz může získat vyšší výkon, pokud počet segmentů ke čtení a jejich celková velikost jsou malé.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Objednáno vs. neobjednaný index columnstore columnstore 
Ve výchozím nastavení pro každou tabulku SQL Analytics vytvořenou bez možnosti indexu vytvoří interní komponenta (tvůrce indexu) neuspořádaný index úložiště sloupců (CCI).  Data v každém sloupci jsou komprimována do samostatného segmentu skupiny řádků CCI.  V rozsahu hodnot každého segmentu jsou metadata, takže segmenty, které jsou mimo hranice predikátu dotazu, se během provádění dotazu nečtou z disku.  CCI nabízí nejvyšší úroveň komprese dat a zmenšuje velikost segmentů ke čtení, takže dotazy mohou běžet rychleji. Protože však tvůrce indexu neseřadí data před jejich kompresí do segmentů, může dojít k segmentům s překrývajícími se rozsahy hodnot, což způsobí, že dotazy budou číst další segmenty z disku a jejich dokončení trvá déle.  

Při vytváření objednané CCI, SQL Analytics stroj seřadí existující data v paměti podle klíče pořadí před tvůrce indexu komprimuje do segmentů indexu.  U seřazených dat je omezení překrývání segmentů, což umožňuje dotazům efektivnější eliminaci segmentů a tím i vyšší výkon, protože počet segmentů pro čtení z disku je menší.  Pokud lze všechna data seřadit v paměti najednou, lze se vyhnout překrývání segmentů.  Vzhledem k velké velikosti dat v tabulkách SQL Analytics se tento scénář nestává často.  

Chcete-li zkontrolovat rozsahy segmentů pro sloupec, spusťte tento příkaz s názvem tabulky a názvem sloupce:

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
> V tabulce objednané CCI jsou nová data vyplývající ze stejné dávky DML nebo operací načítání dat seřazena v rámci této dávky, neexistuje žádné globální řazení mezi všemi daty v tabulce.  Uživatelé mohou znovu sestavit objednané CCI seřadit všechna data v tabulce.  V SQL Analytics columnstore index REBUILD je operace offline.  Pro dělené tabulky REBUILD se provádí jeden oddíl najednou.  Data v oddílu, který je znovu sestaven je "offline" a není k dispozici, dokud rebuild je dokončena pro tento oddíl. 

## <a name="query-performance"></a>Výkon dotazů

Zvýšení výkonu dotazu z objednané CCI závisí na vzorcích dotazu, velikosti dat, způsobu řazení dat, fyzické struktuře segmentů a třídě DWU a prostředku zvolené pro spuštění dotazu.  Uživatelé by měli zkontrolovat všechny tyto faktory před výběrem pořadí sloupců při navrhování objednané tabulky CCI.

Dotazy se všemi těmito vzory obvykle běží rychleji s objednané CCI.  
1. Dotazy mají rovnost, nerovnost nebo rozsah predikáty
1. Sloupce predikátu a seřazené sloupce CCI jsou stejné.  
1. Sloupce predikátu se používají ve stejném pořadí jako řadové pořadí uspořádaných sloupců CCI.  
 
V tomto příkladu tabulka T1 má seskupený columnstore index seřazený v pořadí Col_C, Col_B a Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Výkon dotazu 1 můžete využít více objednané CCI než ostatní tři dotazy. 

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

Výkon načítání dat do objednané tabulky CCI je podobný dělené tabulce.  Načítání dat do objednané tabulky CCI může trvat déle než neobjednaná tabulka CCI z důvodu operace řazení dat, ale dotazy mohou běžet rychleji s objednanými CCI.  

Tady je příklad porovnání výkonu načítání dat do tabulek s různými schématy.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Zde je příklad porovnání výkonu dotazu mezi CCI a objednané CCI.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Zmenšit překrývání segmentů

Počet překrývajících se segmentů závisí na velikosti dat k řazení, dostupné paměti a maximálníst osti paralelismu (MAXDOP) nastavení během uspořádané vytvoření CCI. Níže jsou uvedeny možnosti, jak snížit překrývání segmentů při vytváření objednaného CCI.

- Použijte třídu prostředků xlargerc na vyšší DWU, abyste povolili více paměti pro řazení dat před tím, než tvůrce indexu komprimuje data do segmentů.  Jakmile je v segmentu indexu, nelze změnit fyzické umístění dat.  V segmentu nebo mezi segmenty nejsou žádná data řazení.  

- Vytvořte objednané CCI s MAXDOP = 1.  Každé vlákno používané pro uspořádané vytvoření CCI funguje na podmnožině dat a seřadí je místně.  Neexistuje žádné globální řazení mezi daty seřazené podle různých vláken.  Použití paralelních vláken může zkrátit dobu vytvoření objednané CCI, ale vygeneruje více překrývajících se segmentů než použití jednoho vlákna.  V současné době je možnost MAXDOP podporována pouze při vytváření uspořádané tabulky CCI pomocí příkazu CREATE TABLE AS SELECT.  Vytvoření objednaného CCI pomocí příkazů VYTVOŘIT INDEX nebo VYTVOŘIT TABULKU nepodporuje možnost MAXDOP. Například:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```
- Před seřazením dat podle klíčů řazení před jejich načtením do tabulek SQL Analytics.


Zde je příklad objednané distribuce tabulky CCI, která má nulový segment překrývající se podle výše uvedených doporučení. Objednaná tabulka CCI je vytvořena v databázi DWU1000c prostřednictvím CTAS z tabulky haldy 20 GB pomocí MAXDOP 1 a xlargerc.  CCI je seřazena ve sloupci BIGINT bez duplikátů.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Vytvoření uspořádaného CCI u velkých tabulek
Vytvoření objednané CCI je operace offline.  U tabulek bez oddílů nebudou data přístupná uživatelům, dokud nebude proces vytváření uspořádané kopie cci dokončen.   Pro rozdělené tabulky, protože modul vytvoří objednaný oddíl CCI podle oddílu, uživatelé mohou stále přistupovat k datům v oddílech, kde se nezpracovává uspořádané vytvoření CCI.   Pomocí této možnosti můžete minimalizovat prostoje během seřazeného vytváření CCI u velkých tabulek: 

1.    Vytvořte oddíly v cílové velké tabulce (nazývané Table_A).
2.    Vytvořte prázdnou uspořádanou tabulku CCI (nazvanou Table_B) se stejnou tabulkou a schématem oddílu jako tabulka A.
3.    Přepněte jeden oddíl z tabulky A do tabulky B.
4.    Spusťte index ALTER <Ordered_CCI_Index> <zapnuto Table_B>>> ODDÍL PARTITION_ID> <v tabulce B, abyste znovu vybudovali přepínaný oddíl.  
5.    Opakujte krok 3 a 4 pro každý oddíl v Table_A.
6.    Jakmile jsou všechny oddíly přepnuty z Table_A na Table_B a byly znovu sestaveny, přetažení Table_A a přejmenování Table_B na Table_A. 

## <a name="examples"></a>Příklady

**A. Chcete-li zkontrolovat objednané sloupce a pořadí řadové:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Chcete-li změnit pořadové číslo sloupce, přidejte nebo odeberte sloupce ze seznamu objednávek nebo změnit z CCI na objednané CCI:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v [tématu přehled vývoje](sql-data-warehouse-overview-develop.md).
