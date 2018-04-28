---
title: Zlepšení výkonu index columnstore – Azure SQL Data Warehouse | Microsoft Docs
description: Snižte požadavky na paměť nebo zvýšení dostupné paměti maximalizovat počet řádků, které columnstore index zkomprimuje do každé skupiny řádků.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 909b53e65fd893575a944d714f99698c7e45387d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Tím se maximalizuje quality rowgroup pro columnstore

Kvalita rowgroup je určen podle počtu řádků v skupiny řádků. Snižte požadavky na paměť nebo zvýšení dostupné paměti maximalizovat počet řádků, které columnstore index zkomprimuje do každé skupiny řádků.  Tyto metody používejte ke zlepšení kompresi a dotazování výkonu pro indexy columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Proč na záleží velikost skupiny řádků
Protože columnstore index tabulku kontroluje naskenováním segmenty sloupce s jednotlivých rowgroups, tím se maximalizuje počet řádků v každém rowgroup vylepšuje výkon dotazů. Pokud rowgroups velký počet řádků, zlepšuje komprese dat, což znamená, že je méně dat ke čtení z disku.

Další informace o rowgroups najdete v tématu [Průvodce indexy Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Cílovou velikost rowgroups
Pro nejlepší výkon dotazů cílem je maximalizovat počet řádků na jeden rowgroup v indexu columnstore. Rowgroup může mít maximálně 1 048 576 řádků. Je to v pořádku nemá maximální počet řádků na jeden skupiny řádků. Indexy Columnstore dosáhli dobrého výkonu, pokud rowgroups nejméně 100 000 řádků.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Během komprese můžete získat oříznut Rowgroups

Během hromadné načtení nebo columnstore index opětovném sestavení někdy není k dispozici dostatek paměti pro kompresi všechny řádky, které jsou určené pro každé skupiny řádků. Při přetížení paměti indexy columnstore trim rowgroup velikosti, komprese do columnstore mohlo být úspěšné. 

Pokud nastal nedostatek paměti pro komprimovat alespoň 10 000 řádků do každé skupiny řádků, SQL Data Warehouse, vygeneruje se chyba.

Další informace o hromadné načítání, najdete v části [hromadné načtení do clusterovaný index columnstore](https://msdn.microsoft.com/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-monitor-rowgroup-quality"></a>Postup sledování kvality skupiny řádků

Není DMV (sys.dm_pdw_nodes_db_column_store_row_group_physical_stats), který zveřejňuje užitečné informace, jako je počet řádků v rowgroups a důvody, proč oříznutí, pokud byl ořezávání. Následující zobrazení můžete vytvořit jako užitečný způsob, jak dotaz na tento DMV informace o oříznutí skupiny řádků.

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

Trim_reason_desc oznamuje, zda byl oříznut rowgroup (trim_reason_desc = NO_TRIM znamená došlo bez oříznutí a skupiny řádků je optimální kvality). Oříznutí důvody znamenat předčasné oříznutí rowgroup:
- BULKLOAD: Z tohoto důvodu uvolnění dočasné paměti se používá při příchozí dávku řádků pro zatížení měl menší než 1 milionu řádků. Modul vytvoří skupiny komprimované řádků, pokud jsou větší než 100 000 řádků vkládání (na rozdíl od vkládání do úložiště rozdílová), ale nastaví trim důvod BULKLOAD. V tomto scénáři zvažte zvýšení zatížení okně aplikace batch hromadit další řádky. Navíc přehodnocovat vaše schéma rozdělení oddílů zajistit, že není příliš podrobné jako skupiny řádků nemůžou zahrnovat hranice oddílů.
- MEMORY_LIMITATION: Pokud chcete vytvořit skupiny řádků s 1 milionu řádků, množství paměti pracovní vyžaduje modul. Při načítání relace dostupné paměti je menší než požadovanou paměť pracovní, získat předčasně oříznut skupiny řádků. Následující části popisují, jak k zjištění přibližné hodnoty požadované paměti a přidělení více paměti.
- DICTIONARY_SIZE: Z tohoto důvodu trim označuje, že rowgroup oříznutí došlo k chybě kvůli alespoň jeden sloupec řetězec s širokým nebo vysokou kardinalitou řetězce. Slovník velikost je omezena na 16 MB paměti a je komprimované po dosažení tohoto limitu skupiny řádků. Pokud k této situaci, zvažte možnost odizolování problematické sloupce do samostatné tabulky.

## <a name="how-to-estimate-memory-requirements"></a>Postup odhadnout požadavky na paměť

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Maximální velikost paměti požadované ke zkomprimování jeden rowgroup je přibližně

- 72 MB +
- \#řádky \* \#sloupce \* 8 bajtů +
- \#řádky \* \#krátké. řetězcové sloupce \* 32 bajtů +
- \#Long. řetězcové sloupce \* 16 MB pro slovník komprese

kde krátké. řetězcové sloupce použít datové typy řetězec < = 32 bajtů a datové typy řetězec použití dlouho. řetězcové sloupce > 32 bajtů.

Dlouhé řetězce jsou komprimována pomocí metody komprese určený pro kompresi text. Tato metoda komprese používá *slovník* k uložení textové vzory. Maximální velikost slovník je 16 MB. Není k dispozici pouze jeden slovník pro každý sloupec dlouhý řetězec v skupiny řádků.

Podrobné informace o požadavky na paměť columnstore, najdete v části video [škálování Azure SQL Data Warehouse: Konfigurace a pokyny](https://myignite.microsoft.com/videos/14822).

## <a name="ways-to-reduce-memory-requirements"></a>Způsoby, jak snížit požadavky na paměť

Pomocí následujících postupů snížit požadavky na paměť pro kompresi rowgroups do indexy columnstore.

### <a name="use-fewer-columns"></a>Použití méně sloupců
Pokud je to možné návrh tabulky s menším počtem sloupců. Když do columnstore se komprimují rowgroup, columnstore index komprimaci každý segment sloupce samostatně. Požadavky na paměť zkomprimovat rowgroup proto zvýšení počtu sloupců zvyšuje.


### <a name="use-fewer-string-columns"></a>Použití méně sloupců řetězec
Sloupce řetězce vyžadují větší spotřebu paměti než číselné a datový typ datum. Chcete-li snížit požadavky na paměť, zvažte odebrání řetězcové sloupce z tabulky faktů a jejich uvedení v menší tabulky dimenzí.

Požadavky na další paměť pro kompresi řetězec:

- Datové typy řetězec maximálně 32 znaků může vyžadovat další bajtů 32 za hodnotu.
- Řetězce s více než 32 znaků jsou komprimována pomocí metody slovníku.  Každý sloupec v rowgroup může vyžadovat až dalších 16 MB k vytvoření slovníku.

### <a name="avoid-over-partitioning"></a>Vyhněte se přepsání dělení

Indexy Columnstore vytvořit jeden nebo více rowgroups na jeden oddíl. V SQL Data Warehouse počet oddílů zvětšování rychle vzhledem k tomu, že je distribuován data a každý distribuční je rozdělena na oddíly. Pokud tabulka obsahuje příliš mnoho oddíly, nemusí být k vyplnění rowgroups dostatečný počet řádků. Nedostatečná řádků nevytvoří přetížení paměti při kompresi, ale jeho vede k rowgroups, který nezapojujte nejlepší výkon dotazů columnstore.

Dalším důvodem pro vyhnout přepsání rozdělení oddílů je, že není dostatek paměti režie pro načtení řádků do indexu columnstore v dělenou tabulku. Během zatížení mnoha oddílů může přijímat příchozí řádky, které jsou uložené v paměti, dokud každý oddíl má dostatečný počet řádků, aby se komprimoval. S příliš mnoha oddílů vytvoří přetížení další paměť.

### <a name="simplify-the-load-query"></a>Zjednodušte dotaz zatížení

Databáze sdílí přidělení paměti pro dotaz mezi všechny operátory v dotazu. Při načítání dotazu má rozšířené řazení a spojení, se snižuje paměti k dispozici pro kompresi.

Návrh zatížení dotaz tak, aby se zaměříte jenom na načítání dotazu. Pokud potřebujete spustit transformace dat, spusťte je oddělené od zatížení dotazu. Například příprava data v tabulce haldy, spusťte transformace a pak můžete načíst pracovní tabulky do indexu columnstore. Můžete také nejdřív načíst data a pak pomocí systému MPP transformovat data.

### <a name="adjust-maxdop"></a>Upravit MAXDOP

Každý distribuční zkomprimuje rowgroups do columnstore paralelně, je-li k dispozici na distribučních víc než jednoho jádra procesoru. Paralelismus vyžaduje další paměťové prostředky, které může vést k přetížení paměti a oříznutí skupiny řádků.

Ke snížení přetížení paměti, můžete použít v pomocném parametru dotazu MAXDOP vynutit operace načtení spouštění v režimu sériového portu v rámci každé distribuční.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Způsoby přidělení více paměti

Velikost DWU a třída prostředků uživatele, společně určují, kolik paměti je k dispozici pro dotaz uživatele. Ke zvýšení přidělení paměti pro dotaz zatížení, můžete zvýšit počet Dwu nebo zvýšit Třída prostředků.

- Pokud chcete zvýšit jednotkami Dwu, najdete v části [jak škálování výkonu?](quickstart-scale-compute-portal.md)
- Chcete-li změnit třídy prostředků pro dotaz, [změnit v příkladu třída prostředků uživatele](resource-classes-for-workload-management.md#change-a-users-resource-class).

Například na DWU 100 může uživatel ve třídě prostředků smallrc používat 100 MB paměti pro každý distribuci. Podrobnosti najdete v tématu [souběžnost v SQL Data Warehouse](resource-classes-for-workload-management.md).

Předpokládejme, že zjistíte, je nutné, aby 700 MB paměti pro získání vysoce kvalitní rowgroup velikosti. Tyto příklady ukazují, jak můžete spuštěním dotazu zatížení s dostatek paměti.

- Pomocí DWU 1000 a mediumrc, vaše přidělení paměti je 800 MB
- Pomocí DWU 600 a largerc, vaše přidělení paměti je 800 MB.


## <a name="next-steps"></a>Další postup

Další způsoby, jak zlepšit výkon v SQL Data Warehouse, najdete v tématu [přehled výkonnostní](sql-data-warehouse-overview-manage-user-queries.md).

