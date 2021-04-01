---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92168102"
---
#### <a name="determine-memory-usage"></a>Určení využití paměti 

V části **monitorování** vyberte **protokoly (Analytics)**, zkopírujte následující dotaz telemetrie a vložte ho do okna dotazu a vyberte **Spustit**. Tento dotaz vrátí celkové využití paměti v každém vzorku.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Výsledky vypadají jako v následujícím příkladu:

| časové razítko \[ UTC\]          | name          | hodnota       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14 \. 947 am | Soukromé bajty | 209 932 288 |
| 9/12/2019, 1:06:14 \. 994 am | Soukromé bajty | 212 189 184 |
| 9/12/2019, 1:06:30 \. 010 am | Soukromé bajty | 231 714 816 |
| 9/12/2019, 1:07:15 \. 040 am | Soukromé bajty | 210 591 744 |
| 9/12/2019, 1:12:16 \. 285 am | Soukromé bajty | 216 285 184 |
| 9/12/2019, 1:12:31 \. 376 am | Soukromé bajty | 235 806 720 |

#### <a name="determine-duration"></a>Určit dobu trvání 

Azure Monitor sleduje metriky na úrovni prostředků, což pro funkce je aplikace Function App. Application Insights Integration generuje metriky na základě jednotlivých funkcí. Zde je příklad analytického dotazu, který získá průměrnou dobu trvání funkce:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16 \. 087                     |
| QueueTrigger MaxDurationMs | 90 \. 249                     |
| QueueTrigger MinDurationMs | 8 \. 522                      |
