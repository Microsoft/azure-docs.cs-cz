---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131698"
---
```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|maxBatchSize|64|Maximální počet přijatých událostí za smyčka příjmu.|
|prefetchCount|neuvedeno|Výchozí PrefetchCount, který se použije základní třídy EventProcessorHost.| 
|batchCheckpointFrequency|1|Počet událostí zpracovávaných dávek před vytvořením kontrolního bodu EventHub kurzoru.| 
