---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881335"
---
#### <a name="built-in-log-streaming"></a>Integrované streamování protokolů

`logstream` Pomocí možnosti můžete začít přijímat protokoly streamování konkrétní aplikace Function App běžící v Azure, jako v následujícím příkladu:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Můžete také zobrazit [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) pro aplikaci Function App v novém okně prohlížeče, včetně `--browser` možnosti, jako v následujícím příkladu:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
