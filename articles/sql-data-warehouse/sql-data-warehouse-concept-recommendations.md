---
title: SQL Data Warehouse doporučení
description: Informace o SQL Data Warehouse doporučeních a způsobu jejich vygenerování
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 09dff2c8ddf5b9038aa715cef02e099ccbc68f8a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685920"
---
# <a name="sql-data-warehouse-recommendations"></a>SQL Data Warehouse doporučení

Tento článek popisuje doporučení obsluhovaná SQL Data Warehouse prostřednictvím Azure Advisor.  

SQL Data Warehouse poskytuje doporučení k zajištění konzistentního optimalizace datového skladu pro výkon. Doporučení pro datový sklad jsou úzce integrovaná s [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) , aby vám poskytovala osvědčené postupy přímo v rámci [Azure Portal](https://aka.ms/Azureadvisor). SQL Data Warehouse analyzuje aktuální stav datového skladu, shromažďuje telemetrii a nabízí doporučení pro vaše aktivní úlohy na denní tempo. Podporované scénáře doporučení pro datový sklad jsou popsány níže spolu s postupem použití doporučených akcí.

Pokud máte jakoukoli zpětnou vazbu ke službě SQL Data Warehouse Advisor nebo máte nějaké problémy, nahlaste se [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com).   

Pokud si chcete vyzkoušet doporučení ještě dnes, klikněte [sem](https://aka.ms/Azureadvisor) . Tato funkce je momentálně platná jenom pro Gen2 datové sklady. 

## <a name="data-skew"></a>Zkosit data

Při spuštění úlohy může zkosit data způsobit větší problém při přesunu dat nebo při jejich používání. V následující dokumentaci se dozvíte, jak identifikovat zešikmení dat a zabránit tomu, aby se vybraly výběrem optimálního distribučního klíče.

- [Identifikace a odebrání zkosení](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Žádné nebo zastaralé statistiky

Díky podoptimálním statistikám může mít vážně dopad na výkon dotazů, protože to může způsobit, že Optimalizátor dotazů SQL Data Warehouse vygeneruje neoptimální plány dotazů. Následující dokumentace popisuje osvědčené postupy při vytváření a aktualizaci statistik:

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
