---
title: Doporučení SQL synapse
description: Přečtěte si o doporučeních SQL synapse a způsobu jejich generování.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 17877a1ef5d949fbbee080b6157844ac5b516fe7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633687"
---
# <a name="synapse-sql-recommendations"></a>Doporučení SQL synapse

Tento článek popisuje doporučení SQL synapse, která jsou poskytována prostřednictvím Azure Advisor.  

SQL Analytics poskytuje doporučení k zajištění konzistentního optimalizace úloh datového skladu pro výkon. Doporučení jsou úzce integrovaná s [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) , aby vám poskytovala osvědčené postupy přímo v rámci [Azure Portal](https://aka.ms/Azureadvisor). SQL Analytics shromažďuje doporučení telemetrie a povrchů pro aktivní úlohy na denní tempo. Podporované scénáře doporučení jsou popsány níže spolu s postupem použití doporučených akcí.

Můžete [si vyzkoušet doporučení](https://aka.ms/Azureadvisor) ještě dnes! Tato funkce je momentálně platná jenom pro Gen2 datové sklady.

## <a name="data-skew"></a>Zkosit data

Při spuštění úlohy může zkosit data způsobit větší problém při přesunu dat nebo při jejich používání. V následující dokumentaci se dozvíte, jak identifikovat zešikmení dat a zabránit tomu, aby se vybraly výběrem optimálního distribučního klíče.

- [Identifikace a odebrání zkosení](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Žádné nebo zastaralé statistiky

Díky podoptimálním statistikám může mít vážně dopad na výkon dotazů, protože může způsobit, že Optimalizátor dotazů SQL vygeneruje jak optimální plány dotazů. Následující dokumentace popisuje osvědčené postupy při vytváření a aktualizaci statistik:

- [Vytváření a aktualizace statistiky tabulky](sql-data-warehouse-tables-statistics.md)

Pokud chcete zobrazit seznam ovlivněných tabulek pomocí těchto doporučení, spusťte následující [skript T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Advisor průběžně spouští stejný skript T-SQL, aby vygeneroval tato doporučení.

## <a name="replicate-tables"></a>Replikovat tabulky

V případě doporučení pro replikovanou tabulku detekuje Advisor kandidáty na tabulku na základě následujících fyzických vlastností:

- Velikost replikované tabulky
- Počet sloupců
- Typ distribuce tabulky
- Počet oddílů

Advisor průběžně využívá heuristiky založené na úlohách, jako je četnost přístupů k tabulkám, počet vrácených řádků v průměru a prahové hodnoty velikosti datového skladu a aktivity, abyste zajistili, že se vygenerují vysoce kvalitní doporučení.

Níže jsou popsány heuristicky založené na úlohách, které můžete najít v Azure Portal pro každé z doporučení replikované tabulky:

- Průměrný počet řádků vrácených z tabulky pro každý přístup k tabulce za posledních sedm dní
- Časté čtení bez aktualizace – označuje, že se tabulka během posledních sedmi dnů neaktualizovala při zobrazení aktivity přístupu.
- Poměr pro čtení a aktualizaci – poměr mezi časté, k němuž byla tabulka přístupná, když se aktualizuje za posledních sedm dní
- Activity – měří míru využití na základě aktivity přístupu. Tím se porovná aktivita přístupu k tabulce vzhledem k průměrné aktivitě přístupu k tabulce v rámci datového skladu za posledních sedm dní.

V současné době se u clusterovaných indexů columnstore zobrazí jenom čtyři kandidáty replikované tabulky s upřednostněním nejvyšší aktivity.

> [!IMPORTANT]
> Doporučení replikované tabulky není úplného ověření a nebere v úvahu operace přesunu dat. Pracujeme na tom, abychom tento postup používali jako heuristickou, ale mezitím byste po použití doporučení měli vždy ověřit své zatížení. Pokud si sqldwadvisor@service.microsoft.com vyžádáte doporučení pro replikovanou tabulku, která způsobí, že se vaše úloha vrátí do služby, kontaktujte prosím Další informace o replikovaných tabulkách najdete v následující [dokumentaci](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).
