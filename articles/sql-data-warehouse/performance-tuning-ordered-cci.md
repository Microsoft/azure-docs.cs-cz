---
title: Ladění výkonu pomocí Azure SQL Data Warehouse uspořádaný clusterovaný index columnstore | Microsoft Docs
description: Doporučení a pokyny, které byste měli znát při použití seřazeného clusterovaného indexu columnstore pro zlepšení výkonu dotazů.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 41fbebcf4b85f6e48babba30c2d05fedb3e7a5c7
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985295"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Ladění výkonu pomocí seřazeného clusterovaného indexu columnstore  

Když uživatelé dotazují tabulku columnstore v Azure SQL Data Warehouse, Optimalizátor zkontroluje minimální a maximální hodnoty uložené v jednotlivých segmentech.  Segmenty mimo hranice predikátu dotazu se nečtou z disku do paměti.  Dotaz může dosáhnout rychlejšího výkonu, pokud je počet čtených segmentů a jejich celková velikost malá.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Seřazený a neuspořádaný clusterovaný index columnstore 
Ve výchozím nastavení se pro každou tabulku Azure Data Warehouse vytvořenou bez možnosti indexu vytvoří interní komponenta (Tvůrce indexů) neuspořádaný clusterovaný index columnstore (Ski).  Data v jednotlivých sloupcích jsou komprimována do samostatného segmentu skupiny řádků Ski.  V rozsahu hodnot každého segmentu jsou metadata, takže segmenty, které jsou mimo hranice predikátu dotazu, se při provádění dotazu nečtou z disku.  Ski nabízí nejvyšší úroveň komprese dat a snižuje velikost segmentů ke čtení, aby dotazy mohly běžet rychleji. Vzhledem k tomu, že tvůrce indexů neřadí data před komprimací do segmentů, může dojít k segmentům s překrývajícími se rozsahy hodnot, což způsobí, že dotazy budou číst více segmentů z disku a trvá déle.  

Při vytváření seřazené instrukce Azure SQL Data Warehouse modul seřadí data v paměti pomocí klíčů pořadí, než je tvůrce indexů komprimuje do segmentů indexu.  U seřazených dat je segment překrývající se snížen, takže dotazy mají efektivnější odstraňování segmentů, což znamená rychlejší výkon, protože počet segmentů ke čtení z disku je menší.  Pokud se všechna data dají řadit v paměti najednou, můžete se vyhnout překrývání segmentu.  V případě velké velikosti dat v tabulkách datového skladu k tomuto scénáři nedochází často.  

Chcete-li kontrolovat rozsahy segmentů pro sloupec, spusťte tento příkaz s názvem tabulky a názvem sloupce:

```sql
SELECT o.name, pnp.index_id, pnp.rows, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<table_name>' and c.name = '<column_name>'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id
```

## <a name="data-loading-performance"></a>Výkon načítání dat

Výkon načítání dat do seřazené tabulky Ski je podobný načtení dat do dělené tabulky.  
Načítání dat do seřazené tabulky INSTRUKCí může trvat déle než načítání dat do neuspořádané tabulky INSTRUKCí z důvodu řazení dat.  

Zde je příklad porovnání výkonu načítání dat do tabulek s různými schématy.
![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)
 
## <a name="reduce-segment-overlapping"></a>Zmenšení překrývajících se segmentů
Níže jsou uvedeny možnosti pro další omezení překrývání při vytváření uspořádané konzulární instrukce pro novou tabulku prostřednictvím CTAS nebo v existující tabulce s daty:

- Použijte větší třídu prostředků, abyste umožnili řazení více dat v paměti předtím, než je tvůrce indexů komprimuje do segmentů.  V segmentu indexu nemůže být fyzické umístění dat změněno.  Neexistují žádné řazení dat v rámci segmentu nebo napříč segmenty.  

- Použijte nižší stupeň paralelismu (například DOP = 1).  Každé vlákno používané pro seřazené vytváření konzulárních instrukcí funguje na podmnožině dat a seřadí je místně.  Neexistuje žádné globální řazení napříč daty seřazenými podle různých vláken.  Použití paralelních vláken může zkrátit čas k vytvoření seřazené instrukce, ale vygeneruje více překrývajících se segmentů než použití jednoho vlákna. 
- Před jejich nařazením do Azure SQL Data Warehouse tabulek předem Seřaďte data pomocí klíčů řazení.

## <a name="create-ordered-cci-on-large-tables"></a>Vytváření uspořádané konzulární instrukce pro velké tabulky
Vytvoření seřazené konzulární instrukce je offline operace.  Pro tabulky, které neobsahují oddíly, data nebudou k dispozici uživatelům, dokud se nedokončí proces vytváření řazené Ski.   Pro dělené tabulky, protože modul vytváří seřazený oddíl s pokyny podle oddílu, uživatelé budou mít stále přístup k datům v oddílech, kde se vytváření řazené konzulárních instrukcí nezpracovává.   Tuto možnost můžete použít k minimalizaci výpadku během uspořádaného vytváření Ski v rozsáhlých tabulkách: 

1.  Vytvořte oddíly pro cílovou rozsáhlou tabulku (nazvanou tabulka A).
2.  Vytvoří prázdnou seřazenou tabulku Ski (s názvem tabulka B) se stejným schématem Table a partition jako tabulka a.
3.  Umožňuje přepnout jeden oddíl z tabulky A na tabulku B.
4.  Spuštěním příkazu ALTER INDEX < Ordered_CCI_Index > znovu sestavte v tabulce B a znovu sestavte přepínací oddíl.  
5.  Zopakujte kroky 3 a 4 pro každý oddíl v tabulce A.
6.  Jakmile jsou všechny oddíly přepnuty z tabulky A na tabulku B a znovu sestaveny, odřaďte tabulku A a přejmenujte tabulku B na tabulku A. 

## <a name="examples"></a>Příklady

**A. Chcete-li kontrolovat seřazené sloupce a pořadí pořadí:**
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

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v části [Přehled vývoje SQL Data Warehouse](sql-data-warehouse-overview-develop.md).
