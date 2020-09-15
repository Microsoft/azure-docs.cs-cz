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
ms.openlocfilehash: 83754842eeb4b5d609596045c11451e898960b9a
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064857"
---
### <a name="general-query-limits"></a>Obecné limity dotazů

| Omezení | Popis |
|:---|:---|
| Dotazovací jazyk | Azure Monitor používá stejný [dotazovací jazyk Kusto](/azure/kusto/query/) jako Azure Průzkumník dat. Viz [rozdíly v jazyce Azure monitor protokolu dotazu](../articles/azure-monitor/log-query/data-explorer-difference.md) pro prvky jazyka KQL, které nejsou v Azure monitor podporovány. |
| Oblast Azure | Dotazy protokolů můžou mít nadměrné nároky na to, kdy data jsou Log Analytics pracovní prostory ve více oblastech Azure. Podrobnosti najdete v tématu [omezení dotazů](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Dotazy na různé prostředky | Maximální počet Application Insightsch prostředků a Log Analytics pracovních prostorů v jednom dotazu omezený na 100.<br>Dotaz mezi prostředky není v Návrháři zobrazení podporován.<br>Dotaz na více prostředků v upozorněních protokolu se podporuje v novém rozhraní scheduledQueryRules API.<br>Podrobnosti najdete v tématu [omezení dotazů mezi prostředky](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |

### <a name="user-query-throttling"></a>Omezování uživatelských dotazů
Azure Monitor má několik omezení omezování ochrany proti uživatelům, kteří odesílají nadměrný počet dotazů. Takové chování může potenciálně přetížit systémové prostředky back-endu a ohrozit odezvu služby. Následující omezení jsou navržená tak, aby chránila zákazníky před přerušením a zajistila konzistenci úrovně služeb. Omezení uživatelů a omezení jsou navržena tak, aby ovlivnila pouze extrémní využití a neměla by být relevantní pro obvyklé využití.


| Measure | Omezení na uživatele | Popis |
|:---|:---|:---|
| Souběžné dotazy | 5 | Pokud již existuje 5 dotazů, které jsou spuštěny pro uživatele, všechny nové dotazy jsou umístěny do fronty souběžnosti pro jednotlivé uživatele. Když jeden ze spuštěných dotazů skončí, další dotaz se načte z fronty a spustí se. Nezahrnuje dotazy z pravidel výstrah.
| Čas ve frontě souběžnosti | 3 minuty | Pokud dotaz zůstane ve frontě déle než 3 minuty bez spuštění, bude ukončen chybovou odpovědí HTTP s kódem 429. |
| Celkový počet dotazů ve frontě concurrency | 200 | Až počet dotazů ve frontě dosáhne 200, budou všechny další dotazy odmítnuty s kódem chyby HTTP 429. Toto číslo je navíc k pěti dotazům, které mohou běžet souběžně. |
| Frekvence dotazů | 200 dotazů za 30 sekund | Toto je celková míra, kterou můžou dotazy odeslat jeden uživatel do všech pracovních prostorů.  Toto omezení se vztahuje na programové dotazy nebo dotazy iniciované částmi vizualizace, jako jsou řídicí panely Azure a stránka se souhrnem Log Analytics pracovního prostoru. |

- Optimalizujte své dotazy, jak je popsáno v tématu [optimalizace dotazů protokolu v Azure monitor](../articles/azure-monitor/log-query/query-optimization.md).
- Řídicí panely a sešity mohou obsahovat více dotazů v jednom zobrazení, které generují shluky dotazů při každém načtení nebo aktualizaci. Zvažte jejich rozdělení do více zobrazení, která se načítají na vyžádání. 
- V Power BI zvažte extrakci pouze agregovaných výsledků místo nezpracovaných protokolů.
