---
title: Zvýšení výkonu indexu columnstore pro vyhrazený fond SQL
description: Snižte nároky na paměť nebo Zvětšete dostupnou paměť a maximalizujte počet řádků v rámci jednotlivých skupiny řádků ve vyhrazeném fondu SQL.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 1336359bdd0768ba1d1554554d266cacfb483a43
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566507"
---
# <a name="maximizing-rowgroup-quality-for-columnstore-indexes-in-dedicated-sql-pool"></a>Maximalizace kvality skupiny řádků pro indexy columnstore ve vyhrazeném fondu SQL 

Kvalita skupiny řádků se určuje podle počtu řádků v skupiny řádků. Zvýšení dostupné paměti může maximalizovat počet řádků, které index columnstore komprimuje do každého skupiny řádků.  Tyto metody použijte ke zlepšení míry komprese a výkonu dotazů pro indexy columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Proč skupiny řádků velikost otázek

Vzhledem k tomu, že index columnstore prochází tabulku prohledáním segmentů sloupce jednotlivých rowgroups, maximalizace počtu řádků v každém skupiny řádků vylepšuje výkon dotazů.

Když má rowgroups velký počet řádků, zlepšuje se komprese dat, což znamená, že ke čtení z disku je potřeba méně dat.

Další informace o rowgroups najdete v tématu [Průvodce indexy columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="target-size-for-rowgroups"></a>Cílová velikost pro rowgroups

Pro zajištění nejlepšího výkonu dotazů je cílem maximalizovat počet řádků na skupiny řádků v indexu columnstore. Skupiny řádků může mít maximálně 1 048 576 řádků.

V pořádku není maximální počet řádků na skupiny řádků. Indexy columnstore dosahují dobrého výkonu, pokud rowgroups mají alespoň 100 000 řádků.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Rowgroups se může během komprese zobrazit oříznutím.

Při hromadném načtení nebo opětovném sestavení indexu columnstore není k dispozici dostatek paměti pro komprimaci všech řádků určených pro jednotlivé skupiny řádků. Pokud je k dispozici tlak paměti, indexy columnstore oříznou velikosti skupiny řádků, takže komprimace do columnstore může být úspěšná.

Pokud není dostatek paměti pro komprimaci nejméně 10 000 řádků do každého skupiny řádků, bude vygenerována chyba.

Další informace o hromadném načítání najdete v tématu [hromadné načtení do clusterovaného indexu columnstore](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="how-to-monitor-rowgroup-quality"></a>Jak monitorovat kvalitu skupiny řádků

DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([Sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) obsahuje definici zobrazení odpovídající databázi SQL), která zpřístupňuje užitečné informace, jako je počet řádků v rowgroups a důvod oříznutí, pokud došlo k oříznutí.

Následující zobrazení můžete vytvořit jako praktický způsob dotazování na tento DMV, abyste získali informace o ořezávání skupiny řádků.

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

Trim_reason_desc oznamuje, zda byl skupiny řádků oříznutý (trim_reason_desc = NO_TRIM znamená, že nedošlo k žádnému oříznutí a skupina řádků je optimální kvalita). Následující důvody pro oříznutí označují předčasné oříznutí skupiny řádků:

- BULKLOAD: Tento důvod vystřihování se používá v případě, že příchozí dávka řádků pro zatížení má méně než 1 000 000 řádků. Modul vytvoří komprimované skupiny řádků, pokud je vloženo více než 100 000 řádků (na rozdíl od vložení do rozdílového úložiště), ale nastaví důvod oříznutí na BULKLOAD. V tomto scénáři zvažte zvýšení zatížení dávky tak, aby zahrnovalo více řádků. Také znovu vyhodnoťte schéma dělení, abyste se ujistili, že není příliš podrobný, protože skupiny řádků nemohou zabírat hranice oddílů.
- MEMORY_LIMITATION: Chcete-li vytvořit skupiny řádků s 1 000 000 řádky, je nutné, aby modul vyhodnotil určitou velikost pracovní paměti. Když je dostupná paměť relace načítání menší než požadovaná pracovní paměť, skupiny řádků se předčasně oříznou. Následující části vysvětlují, jak odhadnout požadovanou paměť a přidělit větší množství paměti.
- DICTIONARY_SIZE: Tento důvod vystřihování indikuje, že došlo k skupiny řádků oříznutí, protože existoval alespoň jeden řetězcový sloupec s řetězci velkých a/nebo s vysokými počty mohutnosti. Velikost slovníku je omezená na 16 MB paměti a po dosažení tohoto limitu je skupina řádků komprimovaná. Pokud provedete tuto situaci, zvažte možnost izolovat problematický sloupec do samostatné tabulky.

## <a name="how-to-estimate-memory-requirements"></a>Odhad požadavků na paměť

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Maximální požadovaná paměť pro komprimaci jednoho skupiny řádků je přibližně

- 72 MB +
- \#\* \# sloupce řádků \* 8 bajtů +
- \#řádky \* \# krátkého řetězce – sloupce \* 32 bajtů +
- \#Long-String-Columns \* 16 MB pro kompresní slovník

> [!NOTE]
> Krátké řetězcové sloupce používají řetězcové datové typy <= 32 bajtů a dlouhé-řetězcové sloupce používají řetězcové datové typy > 32 bajtů.

Dlouhé řetězce jsou komprimovány pomocí kompresní metody navržené pro komprimaci textu. Tato metoda komprese používá *slovník* k ukládání textových vzorců. Maximální velikost slovníku je 16 MB. Pro každý sloupec s dlouhým řetězcem v skupiny řádků je k dispozici pouze jeden slovník.

Podrobné informace o požadavcích na paměť columnstore najdete v tématu [škálování vyhrazeného fondu SQL videa: konfigurace a pokyny](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Způsoby snížení požadavků na paměť

Následující postupy použijte ke snížení požadavků na paměť pro komprimaci rowgroups do indexů columnstore.

### <a name="use-fewer-columns"></a>Použít míň sloupců

Pokud je to možné, navrhněte tabulku s méně sloupci. Když je skupiny řádků komprimovaný do columnstore, index columnstore komprimuje každý segment sloupce samostatně.

V takovém případě požadavky na paměť pro komprimaci skupiny řádků se zvyšují podle počtu sloupců.

### <a name="use-fewer-string-columns"></a>Použít míň řetězcové sloupce

Sloupce řetězcových datových typů vyžadují více paměti než číselné a datové typy dat. Pro snížení požadavků na paměť zvažte odebrání sloupců řetězců z tabulek faktů a jejich vložení do menších tabulek dimenzí.

Další požadavky na paměť pro kompresi řetězce:

- Řetězcové datové typy až 32 znaků můžou vyžadovat 32 dalších bajtů na hodnotu.
- Řetězcové datové typy s více než 32 znaky jsou komprimovány pomocí metod Dictionary.  Každý sloupec v skupiny řádků může při sestavování slovníku vyžadovat až dalších 16 MB.

### <a name="avoid-over-partitioning"></a>Vyhnout se přerozdělování

Indexy columnstore vytvoří jeden nebo více rowgroups na oddíl. U vyhrazeného fondu SQL ve službě Azure synapse Analytics počet oddílů rychle roste, protože data jsou distribuována a každá distribuce je rozdělená na oddíly.

Pokud má tabulka příliš mnoho oddílů, nemusí být pro vyplnění rowgroups dostatek řádků. Nedostatek řádků nevytváří tlak paměti během komprese. Ale vede k rowgroups, které nedosahují nejlepšího výkonu dotazů columnstore.

Dalším důvodem pro zamezení přerozdělování do oddílů je zatížení paměti při načítání řádků do indexu columnstore v dělené tabulce.

Během načítání může mnoho oddílů získat vstupní řádky, které jsou uchovávány v paměti, dokud každý oddíl nemá dostatečně komprimovaný řádek. Příliš mnoho oddílů vytváří větší tlak na paměť.

### <a name="simplify-the-load-query"></a>Zjednodušení zátěžového dotazu

Databáze sdílí udělení paměti pro dotaz mezi všemi operátory v dotazu. Když má načtený dotaz složitá řazení a spojení, dojde ke snížení paměti dostupné pro kompresi.

Navrhněte zátěžový dotaz, abyste se mohli zaměřit pouze na načtení dotazu. Pokud potřebujete pro data spustit transformace, spouštějte je odděleně od načteného dotazu. Můžete například připravit data v tabulce haldy, spustit transformace a pak načíst pracovní tabulku do indexu columnstore.

> [!TIP]
> Můžete také načíst data jako první a potom použít systém MPP k transformaci dat.

### <a name="adjust-maxdop"></a>Upravit MAXDOP

Každá distribuce komprimuje rowgroups do columnstore paralelně, pokud je pro jednotlivé distribuce k dispozici více než jeden procesor.

Paralelismus vyžaduje další paměťové prostředky, což může vést k přetížení paměti a oříznutí skupiny řádků.

Chcete-li snížit tlak paměti, můžete použít pomocný parametr dotazu MAXDOP k vynucení spuštění operace načítání v sériovém režimu v rámci každé distribuce.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Způsoby, jak přidělit více paměti

DWU velikost a Třída prostředků uživatele společně určují, kolik paměti je k dispozici pro dotaz na uživatele.

Chcete-li zvýšit udělení paměti pro zátěžový dotaz, můžete buď zvýšit počet DWU nebo zvýšit třídu prostředků.

- Chcete-li zvýšit DWU, přečtěte si téma [návody škálování výkonu?](quickstart-scale-compute-portal.md)
- Chcete-li změnit třídu prostředků pro dotaz, přečtěte si téma [Změna příkladu třídy prostředků uživatele](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Další kroky

Další způsoby, jak vylepšit výkon pro vyhrazený fond SQL, najdete v tématu [Přehled výkonu](cheat-sheet.md).
