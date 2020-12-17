---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f5101233f7995fb58fc530e613ba3235a55c783c
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628671"
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
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTime": ""
            }
        }
    }
}  
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|batchCheckpointFrequency|1|Počet dávek události, které mají být zpracovány před vytvořením kontrolního bodu centra EventHub.|
|eventProcessorOptions/maxBatchSize|10|Maximální počet událostí přijatých pro jednu smyčku příjmu.|
|eventProcessorOptions/prefetchCount|300|Výchozí počet předběžného načtení, který používá základní `EventProcessorHost` . Minimální povolená hodnota je 10.|
|initialOffsetOptions/typ|fromStart|Umístění v datovém proudu událostí, od kterého se má začít zpracovávat, když v úložišti neexistuje kontrolní bod. Možnosti jsou `fromStart` `fromEnd` nebo `fromEnqueuedTime` . `fromEnd` zpracuje nové události, které byly zařazeny do fronty po spuštění aplikace Function App. Platí pro všechny oddíly.  Další informace najdete v [dokumentaci k EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet).|
|initialOffsetOptions/enqueuedTime|–| Určuje čas zařazení události do fronty, ze kterého se má spustit zpracování. Když `initialOffsetOptions/type` je nakonfigurovaný jako `fromEnqueuedTime` , toto nastavení je povinné. Podporuje čas v jakémkoli formátu podporovaném [DateTime. Parse ()](/dotnet/standard/base-types/parsing-datetime), jako je například  `2020-10-26T20:31Z` . Pro přehlednost byste měli zadat také časové pásmo. Pokud není zadané časové pásmo, převezme funkce v místním časovém pásmu počítače, na kterém běží aplikace Function App, což je čas UTC při spuštění v Azure. Další informace najdete v [dokumentaci k EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet).|
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

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|maxBatchSize|64|Maximální počet událostí přijatých pro jednu smyčku příjmu.|
|prefetchCount|neuvedeno|Výchozí předběžné načtení, které bude používat základní `EventProcessorHost` .| 
|batchCheckpointFrequency|1|Počet dávek události, které mají být zpracovány před vytvořením kontrolního bodu centra EventHub.| 

> [!NOTE]
> Odkaz na host.jsv v Azure Functions 1. x najdete v části [host.jsna odkaz pro Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).
