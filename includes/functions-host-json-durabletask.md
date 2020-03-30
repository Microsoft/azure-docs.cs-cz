---
title: zahrnout soubor
description: zahrnout soubor
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117096"
---
Nastavení konfigurace pro [trvalé funkce](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Odolné funkce 1.x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Odolné funkce 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
  }
}

```

Názvy rozbočovačů úloh musí začínat písmenem a skládat pouze z písmen a číslic. Pokud není zadán, výchozí název centra úloh pro aplikaci funkce je **DurableFunctionsHub**. Další informace naleznete v [tématu Centra úloh](../articles/azure-functions/durable-functions-task-hubs.md).

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|název hubu|DurableFunctionsHub|Alternativní [názvy centra úloh](../articles/azure-functions/durable-functions-task-hubs.md) lze izolovat více aplikací trvalé funkce od sebe navzájem, i v případě, že používáte stejný back-end úložiště.|
|controlQueueBatchSize|32|Počet zpráv, které mají být vytahovány z fronty ovládacího prvku současně.|
|controlQueueBufferThreshold|256|Počet zpráv fronty ovládacího prvku, které mohou být uloženy do vyrovnávací paměti v době, kdy dispečer bude čekat před dequeuing všechny další zprávy.|
|partitionCount |4|Počet oddílů pro řídicí frontu. Může být kladné celé číslo mezi 1 a 16.|
|controlQueueVisibilityTimeout |5 minut|Časový limit viditelnosti zpráv fronty ovládacího prvku dequeued.|
|workItemQueueVisibilityTimeout |5 minut|Časový limit viditelnosti zpráv fronty pracovních položek ve frontě.|
|funkce maxConcurrentActivity |10x více než počet procesorů na aktuálním stroji|Maximální počet funkcí aktivity, které mohou být zpracovány souběžně na jedné instanci hostitele.|
|funkce maxConcurrentOrchestrator |10x více než počet procesorů na aktuálním stroji|Maximální počet funkcí orchestrator, které mohou být zpracovány současně na jedné instanci hostitele.|
|maxQueuePollingInterval|30 sekund|Maximální interval dotazování fronty ovládacích a pracovních položek ve formátu *hh:mm:ss.* Vyšší hodnoty může mít za následek vyšší latence zpracování zpráv. Nižší hodnoty mohou vést k vyšším nákladům na úložiště z důvodu zvýšených transakcí úložiště.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Název nastavení aplikace, která má připojovací řetězec Azure Storage slouží ke správě základních prostředků Azure Storage.|
|trackingStoreConnectionStringName||Název připojovacího řetězce, který se má použít pro tabulky Historie a Instance. Pokud není zadán, připojení se `azureStorageConnectionStringName` používá.|
|trackingStoreNamePrefix||Předpona, která má být pro `trackingStoreConnectionStringName` tabulky Historie a Instance používána, pokud je zadána. Pokud není nastavena, bude `DurableTask`výchozí hodnota předpony . Pokud `trackingStoreConnectionStringName` není zadán, pak historie a instance `hubName` tabulky budou používat hodnotu jako `trackingStoreNamePrefix` jejich předponu a jakékoli nastavení pro budou ignorovány.|
|traceInputsAndOutputs |false (nepravda)|Hodnota označující, zda mají sledovat vstupy a výstupy volání funkce. Výchozí chování při sledování události spuštění funkce je zahrnout počet bajtů v serializované vstupy a výstupy pro volání funkce. Toto chování poskytuje minimální informace o tom, co vstupy a výstupy vypadat bez nadýmání protokoly nebo neúmyslně vystavení citlivé informace. Nastavení této vlastnosti na hodnotu true způsobí, že protokolování výchozí funkce zaznamená celý obsah vstupů a výstupů funkcí.|
|logReplayEvents|false (nepravda)|Hodnota označující, zda se má zapsat události přehrání orchestrace do Application Insights.|
|eventGridTopicEndpoint ||Adresa URL koncového bodu vlastního tématu služby Azure Event Grid. Když je tato vlastnost nastavena, události oznámení životního cyklu orchestrace jsou publikovány do tohoto koncového bodu. Tato vlastnost podporuje rozlišení nastavení aplikací.|
|eventGridKeySettingName ||Název nastavení aplikace obsahující klíč používaný k ověřování pomocí vlastního tématu `EventGridTopicEndpoint`služby Azure Event Grid na adrese .|
|eventGridPublishRetryCount|0|Počet opakování, pokud se publikování na téma mřížky událostí nezdaří.|
|eventGridPublishRetryInterval|5 minut|Mřížka událostí publikuje interval opakování ve formátu *hh:mm:ss.*|
|eventGridPublishEventTypes||Seznam typů událostí, které mají být publikovány do mřížky událostí. Pokud není zadán, budou publikovány všechny typy událostí. Povolené hodnoty `Started` `Completed`zahrnují `Failed` `Terminated`, , , .|
|useGracefulShutdown|false (nepravda)|(Náhled) Povolit řádné vypnutí snížit pravděpodobnost selhání spuštění funkce hostitele v procesu.|

Mnoho z těchto nastavení je pro optimalizaci výkonu. Další informace naleznete v [tématu Výkon a měřítko](../articles/azure-functions/durable-functions-perf-and-scale.md).
