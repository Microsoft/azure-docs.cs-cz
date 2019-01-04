---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728978"
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
 > Vlastní měření jsou přidružená k položce telemetrie, do kterých patří. Jsou v souladu s vzorkování s položkou telemetrie obsahující tyto měření. Chcete-li sledovat měření, jehož hodnota je nezávislá na jiné typy telemetrie, použijte [telemetrie metrik](../articles/azure-monitor/app/api-custom-events-metrics.md).

Maximální délka klíče: 150
