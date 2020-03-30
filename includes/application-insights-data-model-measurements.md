---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174765"
---
Kolekce vlastních měření. Tato kolekce slouží k vykazování pojmenovaného měření přidruženého k položce telemetrie. Typické případy použití jsou:
- velikost datové části telemetrie závislostí
- počet položek fronty zpracovaných pomocí telemetrie požadavku
- čas, který zákazník převzal k dokončení kroku v průvodci dokončení události telemetrie.

[Vlastní měření](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) můžete zadat v aplikaci Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Vlastní měření jsou přidružena k položce telemetrie, ke které patří. Podléhají odběru vzorků s položkou telemetrie obsahující tato měření. Chcete-li sledovat měření, které má hodnotu nezávislou na jiných typech telemetrie, použijte [metrickou telemetrii](../articles/azure-monitor/app/api-custom-events-metrics.md).

Maximální délka klíče: 150
