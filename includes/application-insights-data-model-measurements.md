---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 3986b77cfad167134bf8ada1e3cbb73ad64dd3ca
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571959"
---
Kolekce vlastních měření. Použijte tuto kolekci do sestavy s názvem měření přidružené položky telemetrie. Typické případy použití jsou:
- Velikost datové části Telemetrických závislostí
- počet položek fronty zpracovat žádost o Telemetrie
- čas daného zákazníka trvalo dokončení kroku v kroku dokončení Průvodce Telemetrie událostí.

Můžete zadat dotaz [vlastní měření](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) v Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Vlastní měření jsou přidružená k položce telemetrie, do kterých patří. Jsou v souladu s vzorkování s položkou telemetrie obsahující tyto měření. Chcete-li sledovat měření, jehož hodnota je nezávislá na jiné typy telemetrie, použijte [telemetrie metrik](../articles/application-insights/app-insights-api-custom-events-metrics.md).

Maximální délka: 150
