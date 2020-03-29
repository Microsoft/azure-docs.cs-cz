---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174801"
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
|Maxbatchsize|64|Maximální počet událostí přijatých za příjem smyčky.|
|prefetchCount|neuvedeno|Výchozí PrefetchCount, který bude použit podkladové EventProcessorHost.| 
|batchCheckpointFrequency|1|Počet dávek událostí ke zpracování před vytvořením kontrolního bodu kurzoru EventHub.| 
