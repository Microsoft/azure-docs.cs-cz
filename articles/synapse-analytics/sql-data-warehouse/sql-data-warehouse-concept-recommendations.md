---
title: Vyhrazený fond SQL Azure Advisor doporučení
description: Přečtěte si o doporučeních SQL synapse a způsobu jejich generování.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 667629b7f613b11f40528b039c7525339b7a62d0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462856"
---
# <a name="azure-advisor-recommendations-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Advisor doporučení pro vyhrazený fond SQL ve službě Azure synapse Analytics

Tento článek popisuje doporučení vyhrazeného fondu SQL, která jsou k dispozici v Azure Advisor.  

Vyhrazený fond SQL poskytuje doporučení k zajištění konzistentního optimalizace úloh datového skladu pro výkon. Doporučení jsou úzce integrovaná s [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) , aby vám poskytovala osvědčené postupy přímo v rámci [Azure Portal](https://aka.ms/Azureadvisor). Vyhrazený fond SQL shromažďuje doporučení telemetrie a ploch pro vaše aktivní úlohy na denní tempo. Podporované scénáře doporučení jsou popsány níže spolu s postupem použití doporučených akcí.

Můžete [si vyzkoušet doporučení](https://aka.ms/Azureadvisor) ještě dnes! 

## <a name="data-skew"></a>Zkosit data

Při spuštění úlohy může zkosit data způsobit větší problém při přesunu dat nebo při jejich používání. V následující dokumentaci se dozvíte, jak identifikovat zešikmení dat a zabránit tomu, aby se vybraly výběrem optimálního distribučního klíče.

- [Identifikace a odebrání zkosení](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Žádné nebo zastaralé statistiky

Díky podoptimálním statistikám může mít vážně dopad na výkon dotazů, protože může způsobit, že Optimalizátor dotazů SQL vygeneruje jak optimální plány dotazů. Následující dokumentace popisuje osvědčené postupy při vytváření a aktualizaci statistik:

- [Vytváření a aktualizace statistiky tabulky](sql-data-warehouse-tables-statistics.md)

Pokud chcete zobrazit seznam ovlivněných tabulek pomocí těchto doporučení, spusťte následující  [skript T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Advisor průběžně spouští stejný skript T-SQL, aby vygeneroval tato doporučení.

## <a name="replicate-tables"></a>Replikovat tabulky

V případě doporučení pro replikovanou tabulku detekuje Advisor kandidáty na tabulku na základě následujících fyzických vlastností:

- Velikost replikované tabulky
- Počet sloupců
- Typ distribuce tabulky
- Počet oddílů

Advisor průběžně využívá heuristiky založené na úlohách, jako je četnost přístupů k tabulkám, počet vrácených řádků v průměru a prahové hodnoty velikosti datového skladu a aktivity, abyste zajistili, že se vygenerují vysoce kvalitní doporučení.

V následující části jsou popsány heuristicky založené na úlohách, které můžete najít v Azure Portal pro každé z doporučení replikované tabulky:

- Průměrný počet řádků vrácených z tabulky pro každý přístup k tabulce za posledních sedm dní
- Časté čtení bez aktualizace – označuje, že se tabulka během posledních sedmi dnů neaktualizovala při zobrazení aktivity přístupu.
- Poměr pro čtení a aktualizaci – poměr mezi časté, k němuž byla tabulka přístupná, když se aktualizuje za posledních sedm dní
- Activity – měří míru využití na základě aktivity přístupu. Tato aktivita porovnává aktivitu přístupu k tabulce vzhledem k průměrné aktivitě přístupu k tabulce v rámci datového skladu za posledních sedm dní.

V současné době se u clusterovaných indexů columnstore zobrazí jenom čtyři kandidáty replikované tabulky s upřednostněním nejvyšší aktivity.

> [!IMPORTANT]
> Doporučení replikované tabulky není úplného ověření a nebere v úvahu operace přesunu dat. Pracujeme na tom, abychom tento postup používali jako heuristickou, ale mezitím byste po použití doporučení měli vždy ověřit své zatížení. Další informace o replikovaných tabulkách najdete v následující [dokumentaci](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).


## <a name="adaptive-gen2-cache-utilization"></a>Adaptivní (Gen2) využití mezipaměti
Pokud máte velkou pracovní sadu, můžete zaznamenat procento přístupů do mezipaměti a vysoké využití mezipaměti. V tomto scénáři byste měli škálovat až ke zvýšení kapacity mezipaměti a opětovnému spuštění úloh. Další informace najdete v následující [dokumentaci](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache). 

## <a name="tempdb-contention"></a>Obsah databáze tempdb

Výkon dotazů se může snížit, když dojde k vysokému obsahu databáze tempdb.  K obsahu tempdb může dojít prostřednictvím uživatelsky definovaných dočasných tabulek nebo v případě velkého množství přesunu dat. V tomto scénáři můžete škálovat pro další přidělení databáze tempdb a [konfiguraci tříd prostředků a správy úloh](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management) , aby bylo možné získat větší množství paměti pro vaše dotazy. 

## <a name="data-loading-misconfiguration"></a>Nepřesná konfigurace načítání dat

Vždycky byste měli načíst data z účtu úložiště ve stejné oblasti jako vyhrazený fond SQL, abyste minimalizovali latenci. Použijte [příkaz Copy pro příjem dat s vysokou propustností](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) a rozdělte připravené soubory do svého účtu úložiště, aby se maximalizovala propustnost. Pokud nemůžete použít příkaz COPY, můžete pro lepší propustnost použít rozhraní SqlBulkCopy API nebo BCP s vysokou velikostí dávky. Další doprovodné materiály k načítání dat najdete v následující [dokumentaci](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data). 
