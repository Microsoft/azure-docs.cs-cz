---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81791627"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x a novější

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Vlastnost  |Výchozí | Description |
|---------|---------|---------|
|maxBatchSize|10|Maximální počet událostí přijatých pro jednu smyčku příjmu.|
|prefetchCount|300|Výchozí počet předběžného načtení, který používá základní `EventProcessorHost` .|
|batchCheckpointFrequency|1|Počet dávek události, které mají být zpracovány před vytvořením kontrolního bodu centra EventHub.|

> [!NOTE]
> Odkaz na host.jsv v Azure Functions 2. x a novějších verzích najdete v části [host.jsna referenčních údajích pro Azure Functions](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Vlastnost  |Výchozí | Description |
|---------|---------|---------| 
|maxBatchSize|64|Maximální počet událostí přijatých pro jednu smyčku příjmu.|
|prefetchCount|neuvedeno|Výchozí předběžné načtení, které bude používat základní `EventProcessorHost` .| 
|batchCheckpointFrequency|1|Počet dávek události, které mají být zpracovány před vytvořením kontrolního bodu centra EventHub.| 

> [!NOTE]
> Odkaz na host.jsv v Azure Functions 1. x najdete v části [host.jsna odkaz pro Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).

