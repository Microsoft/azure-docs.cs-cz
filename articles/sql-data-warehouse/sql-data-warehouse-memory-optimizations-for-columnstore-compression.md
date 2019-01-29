---
title: Zlepšení výkonu indexu columnstore – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Snižte požadavky na paměť nebo zvýšení dostupné paměti se pro zajištění maximálního počtu řádků, které komprimuje columnstore index do každé skupiny řádků.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: de7cc0e67960edf95ace67808ffc677b57a46dab
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096989"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maximalizace rowgroup kvality columnstore

Skupiny řádků kvality se určuje podle počtu řádků v skupiny řádků. Snižte požadavky na paměť nebo zvýšení dostupné paměti se pro zajištění maximálního počtu řádků, které komprimuje columnstore index do každé skupiny řádků.  Pomocí těchto metod k vylepšení míry komprese a dotazování výkonu pro indexy columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Proč na záleží velikosti skupiny řádků
Protože oddíly indexu columnstore prohledá tabulku tím, že kontroluje segmenty sloupce s jednotlivé rowgroups, maximální počet řádků v každé skupiny řádků vylepšuje výkon dotazů. Když rowgroups mají velký počet řádků, komprese dat zvyšuje, což znamená, že je méně dat pro čtení z disku.

Další informace o rowgroups najdete v tématu [Průvodce indexy Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Velikost cíle pro rowgroups
Pro nejlepší výkon dotazů cílem je maximalizovat počet řádků na skupinu řádků v indexu columnstore. Skupiny řádků může mít maximálně 1 048 576 řádků. Je to v pořádku nebudete chtít maximální počet řádků na skupinu řádků. Indexy Columnstore dosáhli dobrého výkonu při rowgroups mít alespoň 100 000 řádků.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Rowgroups můžete získat oříznut při kompresi

Během hromadné načtení nebo columnstore index opětovné sestavení někdy není dostatek paměti k dispozici pro všechny řádky, které jsou určené pro každé skupiny řádků zkomprimovat. Když je přetížení paměti, indexy columnstore trim velikosti skupiny řádků tak mohlo být úspěšné komprese do indexu columnstore. 

Pokud není dostatek paměti do každé skupiny řádků zkomprimovat alespoň 10 000 řádků, SQL Data Warehouse dojde k chybě.

Další informace o hromadné načítání, najdete v části [hromadné načtení do clusterovaného indexu columnstore](https://msdn.microsoft.com/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-monitor-rowgroup-quality"></a>Jak monitorovat kvalitu skupiny řádků

Není k dispozici zobrazení dynamické správy (sys.dm_pdw_nodes_db_column_store_row_group_physical_stats), která zveřejňuje užitečné informace, jako je počet řádků v rowgroups a důvod ořezávání, pokud byl oříznutí. Následujícím způsobem můžete vytvořit jako šikovný způsob, jak dotazovat tohoto zobrazení dynamické správy k získání informací o oříznutí skupiny řádků.

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

Trim_reason_desc Určuje, zda byla oříznuta skupiny řádků (trim_reason_desc = NO_TRIM znamená došlo bez ořezávání a skupina řádků je optimální kvality). Důvody: uvolnění dočasné paměti znamenat předčasné oříznutí skupiny řádků:
- BULKLOAD: Uvolnění dočasné paměti z tohoto důvodu se používá, kdy příchozí dávku řádků pro zatížení menší než 1 milion řádků. Modul se vytvoří komprimovaná skupina řádků, pokud jsou větší než 100 000 řádků vloženého (na rozdíl od vkládání do obchodu rozdílovou), ale nastaví uvolnění dočasné paměti z důvodu BULKLOAD. V tomto scénáři zvažte zvýšení okno zatížení služby batch, aby akumulují další řádky. Navíc přehodnotit vaše schéma vytváření oddílů a ujistěte se, že není příliš podrobné jako skupiny řádků nemůžou zahrnovat hranice oddílů.
- MEMORY_LIMITATION: Chcete-li vytvořit skupiny řádků s 1 milion řádků, určitá část paměti pracovní vyžaduje modul. Je-li dostupná paměť relace načítání menší než požadované pracovní paměti, získat předčasně oříznut skupiny řádků. Následující části popisují, jak odhadnout paměť požadovanou a přidělení více paměti.
- DICTIONARY_SIZE: Uvolnění dočasné paměti z tohoto důvodu označuje, že došlo k oříznutí rowgroup protože nastal minimálně jeden sloupec řetězce s širokým nebo vysokou kardinalitou řetězce. Velikost slovník je omezena na 16 MB v paměti a je komprimován po dosažení tohoto limitu skupinu řádků. Pokud narazíte na situace, zvažte možnost odizolování problematický sloupec do samostatné tabulky.

## <a name="how-to-estimate-memory-requirements"></a>Tom, jak odhadnout požadavky na paměť

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Maximální velikost paměti požadované jedné skupiny řádků zkomprimovat je přibližně

- 72 MB +
- \#řádky \* \#sloupce \* 8 bajtů +
- \#řádky \* \#krátký řetězec sloupce \* 32 bajtů +
- \#dlouhý řetězec sloupce \* 16 MB pro slovník komprese

kde krátký řetězec sloupce použijte řetězcovými datovými typy z < = 32 bajtů a dlouhé řetězce sloupce použijte řetězcovými datovými typy > 32 bajtů.

Dlouhé řetězce jsou komprimované s metodou komprese určený pro kompresi text. Tato metoda komprese používá *slovníku* pro ukládání textových vzorů. Maximální velikost slovník je 16 MB. Existuje pouze jeden slovník pro každý sloupec dlouhý řetězec v skupiny řádků.

Podrobné informace o požadavky na paměť columnstore, podívejte se na video [škálování Azure SQL Data Warehouse: Konfigurace a pokyny k](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Způsoby, jak snížit požadavky na paměť

Použijte následující postupy ke snížení požadavků na paměť pro kompresi rowgroups do indexy columnstore.

### <a name="use-fewer-columns"></a>Použít méně sloupců
Pokud je to možné Navrhujte v tabulce s menším počtem sloupců. Když skupiny řádků je komprimován do indexu columnstore, columnstore index komprimuje každý segment sloupce samostatně. Proto se požadavky na paměť skupiny řádků zkomprimovat zvýšit jako počtem sloupců.


### <a name="use-fewer-string-columns"></a>Použít méně sloupců řetězec
Sloupce řetězcovými datovými typy vyžadují víc paměti než číselné a datový typ datum. Ke snížení požadavků na paměť, zvažte odebrání sloupce řetězce z tabulky faktů a umístit je do menších tabulky dimenzí.

Požadavky na další paměť pro řetězec komprese:

- 32 bajtů další za hodnota může vyžadovat řetězcovými datovými typy maximálně 32 znaků.
- Řetězcovými datovými typy s více než 32 znaků jsou komprimované, pomocí metody slovníku.  Každý sloupec v skupiny řádků může vyžadovat až další 16 MB k vytvoření slovníku.

### <a name="avoid-over-partitioning"></a>Vyhněte se over-pass-the dělení

Indexy Columnstore vytvořit jeden nebo více rowgroups na oddíl. Ve službě SQL Data Warehouse počet oddílů rychle roste vzhledem k tomu, že data jsou distribuovaná a každé distribuci je rozdělit na oddíly. Pokud tabulka obsahuje příliš mnoho oddílů, nemusí být tak, aby vyplnil rowgroups dostatečný počet řádků. Chybějící řádky nevytváří tlaku na paměť při kompresi, ale povede k rowgroups, které nejsou dosáhnout dotazy vracely co nejlepší columnstore.

Dalším důvodem pro vyhnout over-pass-the dělení je, že je paměť režie pro načítání řádků do indexu columnstore v dělenou tabulku. Při načítání mnoho oddílů může přijímat příchozí řádky, které jsou uložené v paměti, dokud každý oddíl má dostatečný počet řádků, aby se komprimoval. Máte příliš mnoho oddílů znamená větší tlak další paměť.

### <a name="simplify-the-load-query"></a>Zjednodušení dotazu zatížení

Databáze sdílí přidělení paměti pro dotaz mezi všechny operátory v dotazu. Když má dotaz zatížení rozšířené řazení a spojení, se snižuje paměti pro kompresi.

Návrh dotazu zatížení soustředit jenom na načítání dotazu. Pokud je potřeba spouštět transformace dat, je spusťte odděleně od načíst dotazu. Například připraví data v tabulce haldy, spouštět transformace a pak načíst pracovní tabulky do indexu columnstore. Můžete také nejdřív načtěte data a pak systém MPP transformovat data.

### <a name="adjust-maxdop"></a>Adjust MAXDOP

Každé distribuci komprimuje rowgroups do indexu columnstore paralelně při každou distribuce je k dispozici je více než jedno Procesorové jádro. Paralelismus vyžaduje další paměťové prostředky, což může vést k přetížení paměti a oříznutí skupiny řádků.

Pokud chcete snížit zatížení paměti, můžete použít pomocný parametr dotazu MAXDOP vynutit operaci načítání pro spuštění v režimu sériového portu v rámci každé distribuci.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Způsoby, jak přidělit víc paměti

Velikost DWU a třída prostředků uživatele společně určují, kolik paměti je k dispozici pro dotaz uživatele. Pro zvýšení přidělení paměti pro zatížení dotazu, můžete zvýšit počet jednotek Dwu nebo zvýšit třídy prostředků.

- Pokud chcete zvýšit jednotkami Dwu, naleznete v tématu [jak škálovat výkon?](quickstart-scale-compute-portal.md)
- Chcete-li změnit třídy prostředků pro dotaz, [změnit v příkladu třída prostředků uživatele](resource-classes-for-workload-management.md#change-a-users-resource-class).

Například na DWU 100 může uživatel v třídě prostředků smallrc používat 100 MB paměti pro jednotlivé distribuce. Podrobnosti najdete v tématu [souběžnosti ve službě SQL Data Warehouse](resource-classes-for-workload-management.md).

Předpokládejme, že určíte, že potřebujete 700 MB paměti k získání velikosti vysoce kvalitní skupiny řádků. Tyto příklady ukazují, jak můžete spustit dotaz zatížení s dostatek paměti.

- Použití DWU 1000 a mediumrc, vaše přidělení paměti je 800 MB
- Použití DWU 600 a largerc, vaše přidělení paměti je 800 MB.


## <a name="next-steps"></a>Další postup

Další způsoby, jak zvýšit výkon služby SQL Data Warehouse, najdete v tématu [přehled výkonu](sql-data-warehouse-overview-manage-user-queries.md).

