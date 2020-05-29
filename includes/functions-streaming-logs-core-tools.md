---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881335"
---
#### <a name="built-in-log-streaming"></a>Integrované streamování protokolů

Pomocí možnosti `logstream` spustíte příjem protokolů streamování konkrétní aplikace funkcí běžící v Azure, jako v následujícím příkladu:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Můžete pro svou aplikaci funkcí také zobrazit [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) v novém okně prohlížeče zahrnutím možnosti `--browser`, jak je uvedeno v následujícím příkladu:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
