---
title: Protokoly a metriky v Azure | Microsoft Docs
description: Přehled diagnostických protokolů v Azure, které poskytují bohatou a častou data o provozu prostředku Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262359"
---
# <a name="logs-and-metrics-in-azure"></a>Protokoly a metriky v Azure
Existují různé [protokoly](data-platform-logs.md) a [metriky](data-platform-metrics.md) .

Monitorování aplikací a služeb v Azure se dá rozdělit do těch, které jsou uložené v [datové platformě Azure](data-platform.md). 

Protokoly a metriky je možné rozdělit do dvou kategorií.

- Protokoly platforem a metriky, které jsou automaticky generovány bez nutnosti jiné konfigurace než jednoduše 



| Vrstva | Protokoly platformy | Metriky platformy | Vlastní protokoly | Vlastní metriky |
|:---|:---|:---|:---|:---|
| Aplikace  | | | | |
| Hostovaný operační systém     | Prezenční signál |  | Rozšíření diagnostiky<br>Agent Log Analytics | Rozšíření diagnostiky |
| Resource     | [Protokoly prostředků](resource-logs-overview.md)<br>(specifické pro každou službu) | [Metriky prostředků](metrics-supported.md)<br>(specifické pro každou službu) | | [Vlastní metriky](metrics-custom-overview.md) |
| Subscription | [Protokol aktivit](activity-logs-overview.md) | | | |
| Tenant       | 

## <a name="next-steps"></a>Další kroky

* [Stream protokolů diagnostiky prostředků k **Event Hubs**](resource-logs-stream-event-hubs.md)
* [Změnit nastavení diagnostiky prostředků pomocí REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)
* [Analýza protokolů z Azure Storage pomocí Azure Monitor](collect-azure-metrics-logs.md)
