---
title: zahrnout soubor
description: zahrnout soubor
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072633"
---
### <a name="general-query-limits"></a>Obecná omezení dotazů

| Omezení | Popis |
|:---|:---|
| Dotazovací jazyk | Azure Monitor používá stejný [dotazovací jazyk Kusto](/azure/kusto/query/) jako Průzkumník dat Azure. Viz [Rozdíly v dotazovacím jazyce protokolu Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) pro prvky jazyka KQL, které nejsou ve službě Azure Monitor podporované. |
| Oblast Azure | Dotazy protokolu může dojít k nadměrné režii, když data zahrnuje log Analytics pracovníprostory ve více oblastech Azure. Podrobnosti najdete v [tématu Omezení dotazů.](../articles/azure-monitor/log-query/scope.md#query-limits) |
| Dotazy na křížové prostředky | Maximální počet prostředků Application Insights a pracovních prostorů Analýzy protokolů v jednom dotazu omezeném na 100.<br>V návrháři zobrazení není podporován dotaz mezi prostředky.<br>Dotaz mezi prostředky ve výstrahách protokolu je podporován v novém rozhraní API scheduledQueryRules.<br>Podrobnosti najdete [v tématu Limity dotazů mezi zdroji.](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) |

### <a name="user-query-throttling"></a>Omezení dotazů uživatelů
Azure Monitor má několik omezení omezení k ochraně proti uživatelům odesílání nadměrného počtu dotazů. Takové chování může potenciálně přetížit prostředky back-endu systému a ohrozit odezvu služby. Následující limity jsou navrženy tak, aby chránily zákazníky před přerušeními a zajišťovaly konzistentní úroveň služeb. Omezení a omezení uživatele jsou navrženy tak, aby ovlivnily pouze scénář extrémního použití a neměly by být relevantní pro typické použití.


| Measure | Limit na uživatele | Popis |
|:---|:---|:---|
| Souběžné dotazy | 5 | Pokud již existuje 5 dotazů spuštěných pro uživatele, všechny nové dotazy jsou umístěny ve frontě souběžnosti pro jednotlivé uživatele. Po ukončení jednoho z spuštěných dotazů bude další dotaz stažen z fronty a spuštěn. To nezahrnuje dotazy z pravidel výstrah.
| Čas ve frontě souběžnosti | 2,5 minuty | Pokud dotaz je ve frontě více než 2,5 minuty bez spuštění, bude ukončen s chybovou odpovědí HTTP s kódem 429. |
| Celkový počet dotazů ve frontě souběžnosti | 40 | Jakmile počet dotazů ve frontě dosáhne 40, všechny další dotazy budou odmítnuty s kódem chyby HTTP 429. Toto číslo je navíc k 5 dotazů, které lze spustit současně. |
| Míra dotazů | 200 dotazů za 30 sekund | Jedná se o celkovou rychlost, kterou může dotaz odeslat jeden uživatel do všech pracovních prostorů.  Toto omezení platí pro programové dotazy nebo dotazy iniciované vizualizačními částmi, jako jsou řídicí panely Azure a stránka souhrnu pracovního prostoru Analýzy protokolů. |

- Optimalizujte své dotazy, jak je popsáno v [optimalizaci dotazů protokolu v Azure Monitoru](../articles/azure-monitor/log-query/query-optimization.md).
- Řídicí panely a sešity mohou obsahovat více dotazů v jednom zobrazení, které generují shluk dotazů při každém načtení nebo aktualizaci. Zvažte jejich rozdělení do více zobrazení, které se načítají na vyžádání. 
- V Power BI zvažte extrahování jenom agregované výsledky spíše než nezpracované protokoly.