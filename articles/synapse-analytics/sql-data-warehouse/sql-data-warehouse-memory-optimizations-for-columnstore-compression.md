---
title: Zlepšení výkonu indexu columnstore
description: Snižte požadavky na paměť nebo zvyšte dostupnou paměť, abyste maximalizovali počet řádků v rámci každé skupiny řádků.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d2cbe9b94c4698a93b93c032ee4dcb421a78e59b
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743067"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maximalizace kvality skupiny řádků pro columnstore

Kvalita skupiny řádků je určena počtem řádků ve skupině řádků. Zvýšení dostupné paměti může maximalizovat počet řádků, které index columnstore komprimuje do každé skupiny řádků.  Pomocí těchto metod můžete zlepšit míru komprese a výkon dotazů pro indexy columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Proč záleží na velikosti skupiny řádků

Vzhledem k tomu, že index columnstore prohledává tabulku skenováním segmentů sloupců jednotlivých skupin řádků, maximalizace počtu řádků v každé skupině řádků zvýší výkon dotazu.

Pokud mají skupiny řádků vysoký počet řádků, komprese dat se zlepší, což znamená, že je méně dat ke čtení z disku.

Další informace o skupinách řádků naleznete v [příručce Columnstore Indexes Guide](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="target-size-for-rowgroups"></a>Velikost cíle pro skupiny řádků

Pro nejlepší výkon dotazu je cílem maximalizovat počet řádků na skupinu řádků v indexu columnstore. Skupina řádků může mít maximálně 1 048 576 řádků.

Je v pořádku nemít maximální počet řádků na skupinu řádků. Columnstore indexy dosáhnout dobrého výkonu, pokud rowgroups mají alespoň 100 000 řádků.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Rowgroups může dostat oříznuté během komprese

Během hromadné zatížení nebo columnstore index znovu sestavit, někdy není dostatek paměti k dispozici komprimovat všechny řádky určené pro každou skupinu řádků. Když je přítomen tlak paměti, indexy columnstore oříznou velikosti skupiny řádků, takže komprese do columnstore může být úspěšná.

Pokud není dostatek paměti pro kompresi alespoň 10 000 řádků do každé skupiny řádků, bude generována chyba.

Další informace o hromadném načítání naleznete v [tématu Hromadné načtení do clusterovaného indexu columnstore](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="how-to-monitor-rowgroup-quality"></a>Jak sledovat kvalitu rowgroup

DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats[(sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) obsahuje definici zobrazení odpovídající SQL DB), která zveřejňuje užitečné informace, jako je například počet řádků ve skupinách řádků a důvod oříznutí, pokud došlo k oříznutí.

Můžete vytvořit následující zobrazení jako praktický způsob, jak dotaz na tento DMV získat informace o oříznutí rowgroup.

```sql
create view dbo.vCS_rg_physical_stats
as
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]
)
select *
from cte;
```

Trim_reason_desc říká, zda byla skupina řádků oříznuta(trim_reason_desc = NO_TRIM znamená, že nedošlo k oříznutí a skupina řádků je optimální kvality). Následující důvody oříznutí označují předčasné oříznutí skupiny řádků:

- BULKLOAD: Tento důvod oříznutí se používá, když příchozí dávka řádků pro zatížení měla méně než 1 milion řádků. Modul vytvoří komprimované skupiny řádků, pokud jsou více než 100 000 řádků, které jsou vloženy (na rozdíl od vložení do úložiště delta), ale nastaví důvod oříznutí bulkload. V tomto scénáři zvažte zvýšení zatížení dávky zahrnout další řádky. Také přehodnotit schéma dělení, aby bylo zajištěno, že není příliš podrobné, protože skupiny řádků nemohou span hranice oddílu.
- MEMORY_LIMITATION: Chcete-li vytvořit skupiny řádků s 1 milion řádků, je motorem vyžadováno určité množství pracovní paměti. Pokud je dostupná paměť relace načítání menší než požadovaná pracovní paměť, skupiny řádků se předčasně oříznou. V následujících částech je vysvětleno, jak odhadnout požadovanou paměť a přidělit více paměti.
- DICTIONARY_SIZE: Tento důvod oříznutí označuje, že došlo k oříznutí skupiny řádků, protože byl alespoň jeden sloupec řetězce s širokými a/nebo vysokými řetězci mohutnosti. Velikost slovníku je omezena na 16 MB v paměti a po dosažení tohoto limitu je skupina řádků komprimována. Pokud narazíte na tuto situaci, zvažte izolaci problematické sloupce do samostatné tabulky.

## <a name="how-to-estimate-memory-requirements"></a>Jak odhadnout požadavky na paměť

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Maximální požadovaná paměť pro kompresi jedné skupiny řádků je přibližně

- 72 MB +
- \#řádky \* \# \* sloupce 8 bajtů +
- \#řádky \* \#krátkých sloupců 32 \* bajtů +
- \#sloupce s dlouhým \* řetězcem 16 MB pro kompresní slovník

> [!NOTE]
> Sloupce s krátkým řetězcem používají datové typy řetězců <= 32 bajtů a sloupce s dlouhým řetězcem používají datové typy řetězců > 32 bajtů.

Dlouhé řetězce jsou komprimovány metodou komprese určenou pro kompresi textu. Tato metoda komprese používá *slovník* k ukládání textových vzorů. Maximální velikost slovníku je 16 MB. Pro každý sloupec dlouhého řetězce ve skupině řádků existuje pouze jeden slovník.

Podrobné informace o požadavcích na paměť columnstore najdete v tématu video [Synapse SQL pool scaling: konfigurace a pokyny](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Způsoby, jak snížit požadavky na paměť

Pomocí následujících postupů snížit požadavky na paměť pro kompresi rowgroups do columnstore indexy.

### <a name="use-fewer-columns"></a>Použití menšího počtu sloupců

Pokud je to možné, navrhněte tabulku s menším počtem sloupců. Když je skupina řádků komprimována do columnstore, index columnstore komprimuje každý segment sloupce samostatně.

Jako takové požadavky na paměť komprimovat skupiny řádků zvýšit s počtem sloupců zvyšuje.

### <a name="use-fewer-string-columns"></a>Použití menšího počtu sloupců řetězce

Sloupce datových typů řetězců vyžadují více paměti než číselné datové typy a datové typy kalendářních dat. Chcete-li snížit požadavky na paměť, zvažte odebrání sloupců řetězce z tabulek faktů a jejich umístění do tabulek menších dimenzí.

Další požadavky na paměť pro kompresi řetězce:

- Řetězce datových typů až 32 znaků může vyžadovat 32 dalších bajtů na hodnotu.
- Řetězcové datové typy s více než 32 znaky jsou komprimovány pomocí slovníkových metod.  Každý sloupec ve skupině řádků může vyžadovat až dalších 16 MB k vytvoření slovníku.

### <a name="avoid-over-partitioning"></a>Vyhněte se nadměrnému dělení

Columnstore indexy vytvořit jeden nebo více rowgroups na oddíl. Pro fond SQL v Azure Synapse Analytics počet oddílů rychle roste, protože data jsou distribuovány a každá distribuce je rozdělena na oddíly.

Pokud tabulka obsahuje příliš mnoho oddílů, nemusí být dostatek řádků k vyplnění skupin řádků. Nedostatek řádků nevytváří tlak paměti během komprese. Ale vede k rowgroups, které nedosahují nejlepší columnstore dotaz u výkonu.

Dalším důvodem, proč se vyhnout nadměrnému dělení, je režie paměti pro načítání řádků do indexu columnstore v rozdělené tabulce.

Během zatížení může mnoho oddílů přijímat příchozí řádky, které jsou drženy v paměti, dokud každý oddíl nemá dostatek řádků ke stlačení. S příliš mnoho oddílů vytvoří další tlak paměti.

### <a name="simplify-the-load-query"></a>Zjednodušení dotazu na načtení

Databáze sdílí udělení paměti pro dotaz mezi všechny operátory v dotazu. Pokud má dotaz na zatížení komplexní řazení a spojení, sníží se paměť, která je k dispozici pro kompresi.

Navrhněte dotaz na načtení, který se bude zaměřovat pouze na načítání dotazu. Pokud potřebujete spustit transformace na data, spusťte je odděleně od dotazu na načtení. Například fázi data v tabulce haldy, spusťte transformace a načtěte pracovní tabulku do indexu columnstore.

> [!TIP]
> Můžete také nejprve načíst data a potom použít systém MPP k transformaci dat.

### <a name="adjust-maxdop"></a>Upravit MAXDOP

Každá distribuce komprimuje rowgroups do columnstore paralelně, pokud je k dispozici více než jedno jádro procesoru na distribuci.

Paralelismus vyžaduje další paměťové prostředky, což může vést k tlaku paměti a oříznutí skupiny řádků.

Chcete-li snížit tlak paměti, můžete použít nápovědu dotazu MAXDOP k vynucení spuštění operace zatížení v sériovém režimu v rámci každé distribuce.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Způsoby přidělení více paměti

Velikost DWU a třída prostředků uživatele společně určují, kolik paměti je k dispozici pro dotaz uživatele.

Chcete-li zvýšit udělení paměti pro dotaz na načtení, můžete buď zvýšit počet DWU nebo zvýšit třídu prostředků.

- Chcete-li zvýšit du-li zvýšit, přečtěte si další informace o [tom, jak lze změnit výkon?](quickstart-scale-compute-portal.md)
- Pokud chcete změnit třídu prostředků pro dotaz, přečtěte si příklad [změny třídy prostředků uživatele](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Další kroky

Další způsoby, jak zlepšit výkon fondu SQL, naleznete v [přehledu výkonu](cheat-sheet.md).
