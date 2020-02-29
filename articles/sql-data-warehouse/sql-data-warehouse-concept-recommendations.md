---
title: Doporučení pro analýzu SQL
description: Přečtěte si o doporučeních SQL Analytics a způsobu jejich generování.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 5471236c09737eeef2d4cb7542c245d3087e726c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195953"
---
# <a name="sql-analytics-recommendations"></a>Doporučení pro analýzu SQL

Tento článek popisuje doporučení SQL Analytics, která jsou poskytována prostřednictvím Azure Advisor.  

SQL Analytics poskytuje doporučení k zajištění konzistentního optimalizace úloh datového skladu pro výkon. Doporučení jsou úzce integrovaná s [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) , aby vám poskytovala osvědčené postupy přímo v rámci [Azure Portal](https://aka.ms/Azureadvisor). SQL Analytics shromažďuje doporučení telemetrie a povrchů pro aktivní úlohy na denní tempo. Podporované scénáře doporučení jsou popsány níže spolu s postupem použití doporučených akcí.

Můžete [si vyzkoušet doporučení](https://aka.ms/Azureadvisor) ještě dnes! Tato funkce je momentálně platná jenom pro Gen2 datové sklady. 

## <a name="data-skew"></a>Zkosit data

Při spuštění úlohy může zkosit data způsobit větší problém při přesunu dat nebo při jejich používání. V následující dokumentaci se dozvíte, jak identifikovat zešikmení dat a zabránit tomu, aby se vybraly výběrem optimálního distribučního klíče.

- [Identifikace a odebrání zkosení](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Žádné nebo zastaralé statistiky

Díky podoptimálním statistikám může mít vážně dopad na výkon dotazů, protože může způsobit, že Optimalizátor dotazů SQL vygeneruje jak optimální plány dotazů. Následující dokumentace popisuje osvědčené postupy při vytváření a aktualizaci statistik:

- [Vytváření a aktualizace statistiky tabulky](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

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
> Doporučení replikované tabulky není úplného ověření a nebere v úvahu operace přesunu dat. Pracujeme na tom, abychom tento postup používali jako heuristickou, ale mezitím byste po použití doporučení měli vždy ověřit své zatížení. Pokud si vyžádáte doporučení replikovaných tabulek, která způsobí, že se vaše úloha vrátí do, kontaktujte prosím sqldwadvisor@service.microsoft.com. Další informace o replikovaných tabulkách najdete v následující [dokumentaci](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
