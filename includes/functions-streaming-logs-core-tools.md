---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
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
