---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "67174765"
---
Kolekce vlastních měření Pomocí této kolekce můžete nahlásit pojmenovanou měření přidruženou k položce telemetrie. Typické případy použití:
- Velikost datové části telemetrie závislosti
- počet položek fronty zpracovaných telemetrie požadavků
- čas potřebný k dokončení kroku v rámci telemetrie událostí dokončení kroku průvodce

Můžete zadávat dotazy na [vlastní měření](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) v analýze aplikací:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Vlastní měření jsou přidružená k položce telemetrie, ke které patří. Podléhají vzorkování s položkou telemetrie obsahující tato měření. Chcete-li sledovat měření, která má hodnotu nezávislou na jiných typech telemetrie, použijte [telemetrii metriky](../articles/azure-monitor/app/api-custom-events-metrics.md).

Maximální délka klíče: 150
