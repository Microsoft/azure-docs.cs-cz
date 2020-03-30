---
title: Doporučení sql analytics
description: Informace o doporučeních SQL Analytics a o jejich generování
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
ms.openlocfilehash: 6d57fdb035e076c75363d23fbf36d39eeb72bb3f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350693"
---
# <a name="sql-analytics-recommendations"></a>Doporučení sql analytics

Tento článek popisuje doporučení SQL Analytics obsluhované prostřednictvím Azure Advisor.  

SQL Analytics poskytuje doporučení, která zajistí, že vaše úloha datového skladu je konzistentně optimalizovaná pro výkon. Doporučení jsou úzce integrovaná s [Azure Advisorem,](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) aby vám poskytla osvědčené postupy přímo na [webu Azure Portal](https://aka.ms/Azureadvisor). SQL Analytics shromažďuje telemetrická data a zobrazuje doporučení pro vaše aktivní úlohy v denní kadenci. Podporované scénáře doporučení jsou uvedeny níže spolu s tím, jak použít doporučené akce.

Svá [doporučení si](https://aka.ms/Azureadvisor) můžete ověřit ještě dnes! V současné době je tato funkce použitelná pouze pro datové sklady Gen2. 

## <a name="data-skew"></a>Zkosení dat

Zkosení dat může způsobit další přesun dat nebo problémová místa prostředků při spuštění úlohy. Následující dokumentace popisuje zobrazit identifikovat zkosení dat a zabránit tomu, aby se to stalo výběrem optimální distribuční klíč.

- [Identifikace a odebrání zkosení](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Žádné nebo zastaralé statistiky

S neoptimální statistiky může vážně ovlivnit výkon dotazu, protože může způsobit optimalizace dotazů SQL generovat neoptimální plány dotazů. Následující dokumentace popisuje osvědčené postupy týkající se vytváření a aktualizace statistik:

- [Vytváření a aktualizace statistik tabulky](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Chcete-li zobrazit seznam ovlivněných tabulek podle těchto doporučení, spusťte následující [skript T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Poradce průběžně spouští stejný skript T-SQL pro generování těchto doporučení.

## <a name="replicate-tables"></a>Replikovat tabulky

U doporučení replikované tabulky poradce zjistí kandidáty v tabulce na základě následujících fyzických charakteristik:

- Velikost replikované tabulky
- Počet sloupců
- Typ rozložení tabulky
- Počet oddílů

Advisor neustále využívá heuristiky založené na úlohách, jako je frekvence přístupu k tabulce, řádky vrácené v průměru a prahové hodnoty kolem velikosti datového skladu a aktivity, aby zajistily generování vysoce kvalitních doporučení. 

Následující text popisuje heuristiku založenou na úlohách, kterou můžete najít na webu Azure Portal pro každé doporučení replikované tabulky:

- Prohledat avg- průměrné procento řádků, které byly vráceny z tabulky pro každý přístup k tabulce za posledních sedm dní
- Časté čtení, žádná aktualizace - označuje, že tabulka nebyla aktualizována v posledních sedmi dnech při zobrazení aktivity přístupu
- Poměr čtení a aktualizace – poměr četnosti přístupu k tabulce vzhledem k aktualizaci za posledních sedm dní
- Aktivita - měří využití na základě aktivity přístupu. To porovnává aktivitu přístupu k tabulce vzhledem k průměrné aktivitě přístupu k tabulce v datovém skladu za posledních sedm dní. 

V současné době poradce zobrazí pouze maximálně čtyři replikované tabulka kandidátů najednou s clusterované columnstore indexy upřednostňující nejvyšší aktivitu.

> [!IMPORTANT]
> Doporučení replikované tabulky není úplným důkazem a nebere v úvahu operace přesunu dat. Pracujeme na přidání jako heuristické, ale do té doby byste měli vždy ověřit své pracovní vytížení po použití doporučení. Obraťte sqldwadvisor@service.microsoft.com se, pokud zjistíte doporučení replikované tabulky, která způsobí, že vaše úlohy k regress. Další informace o replikovaných tabulkách naleznete v následující [dokumentaci](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
