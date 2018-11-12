---
title: Doporučení – koncepty služby SQL Data Warehouse | Dokumentace Microsoftu
description: Další informace o doporučení SQL Data Warehouse a jak se generují
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 712eed36f3a68ee02668849207835e3c8bdb8238
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232150"
---
# <a name="sql-data-warehouse-recommendations"></a>Doporučení pro SQL Data Warehouse

Tento článek popisuje doporučení obsluhuje SQL Data Warehouse pomocí Azure Advisoru.  

SQL Data Warehouse poskytuje doporučení, aby váš datový sklad je trvale optimalizován pro výkon. Datový sklad doporučení jsou úzce integrovaná s [Azure Advisoru](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) , kde přinášejí osvědčené postupy přímo v rámci [webu Azure portal](https://aka.ms/Azureadvisor). SQL Data Warehouse analyzuje aktuální stav služby data warehouse, shromažďuje telemetrická data a zařízení Surface doporučení pro aktivní úlohy na denní bázi. Scénáře podporovaná data warehouse doporučení jsou popsány níže spolu s jak použít doporučené akce.

Pokud máte nějakou zpětnou vazbu na Poradce pro SQL Data Warehouse nebo setkáte s problémy, kontaktujte [ sqldwadvisor@service.microsoft.com ](mailto:sqldwadvisor@service.microsoft.com).   

Klikněte na tlačítko [tady](https://aka.ms/Azureadvisor) zkontrolujte svá doporučení ještě dnes! Aktuálně tato funkce se vztahuje na Gen2 pouze datové sklady. 

## <a name="data-skew"></a>Nerovnoměrná distribuce dat

Nerovnoměrná distribuce dat může způsobit další data pohybu nebo prostředků problémová místa při spuštění vaší úlohy. Následující dokumentace popisuje zobrazit identifikovat Nerovnoměrná distribuce dat a zabránit ho tak, že vyberete optimální distribučního klíče.

- [Zjistit a odebrat Nerovnoměrná distribuce](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Ne nebo jsou zastaralé statistiky

Budete mít statistiky neoptimální může vážně ovlivnit výkon dotazů, protože to může způsobit optimalizátoru dotazů SQL Data Warehouse ke generování plány neoptimální dotazů. Následující dokumentace popisuje osvědčené postupy týkající se vytváření a aktualizaci statistiky:

- [Vytváření a aktualizaci statistik tabulek](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Pokud chcete zobrazit seznam ovlivněných tabulek pomocí těchto doporučení, spusťte následující příkaz [skriptu T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Advisor nepřetržitě spouští stejný skript T-SQL ke generování těchto doporučení.

## <a name="replicate-tables"></a>Replikovat tabulky

Replikované tabulky doporučení služby Advisor detekuje tabulky kandidáty na základě následující fyzické charakteristiky:

- Replikované tabulky velikostí
- Počet sloupců
- Typ distribuce tabulky
- Počet oddílů

Advisor průběžně využívá heuristiky založený na úlohách, jako je četnost přístupu tabulky, v průměru vráceny řádky a prahové hodnoty kolem údajů o skladu velikost a aktivitu pro Ujistěte se, že jsou generovány vysoce kvalitní doporučení. 

Následující část popisuje heuristickými metodami založený na úlohách, mohou najít na webu Azure Portal pro každou replikovanou tabulku doporučení:

- Kontrola avg-průměrné procento řádků vrácených z tabulky pro každý přístup k tabulce za posledních 7 dnů
- Častá čtení, není pro sadu vs11 – označuje, že tabulka nebyla aktualizována v posledních sedmi dnech při zobrazování aktivit přístupu
- Číst/aktualizovat poměr - poměr jak často se použila v tabulce vzhledem k jeho aktualizace za posledních sedm dnů
- Aktivita – měří využití na základě aktivity přístupu. To porovnává aktivit přístupu tabulky vzhledem k aktivity přístupu k tabulce v datovém skladu za posledních sedm dnů. 

Aktuálně služby Advisor zobrazí pouze maximálně čtyři replikované tabulky kandidáty najednou v clusterovaných indexech columnstore priorit nejvyšší aktivity.

> [!IMPORTANT]
> Doporučení replikované tabulky není úplné testování a nebere operace přesunu dat účtu. Pracujeme na přidání tohoto jako heuristické metody, ale do té doby by měla vždy ověřovaly vašich úloh po použití doporučení. Obraťte se prosím na sqldwadvisor@service.microsoft.com při zjištění doporučení replikované tabulky, které způsobí, že úlohy chcete vrátit. Další informace o replikované tabulky, navštivte následující [dokumentaci](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
>
