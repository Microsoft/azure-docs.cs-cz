---
title: zahrnout soubor
description: zahrnout soubor
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 54ce9438f768e347e306432a1874ab1816a1ae95
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719730"
---
Nastavení konfigurace pro [Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md).

> [!NOTE]
> Všechny hlavní verze Durable Functions jsou podporovány ve všech verzích modulu runtime Azure Functions. Schéma host.jsv konfiguraci se ale mírně liší v závislosti na verzi Azure Functions runtime a používané verzi rozšíření Durable Functions. Následující příklady slouží pro použití s Azure Functions 2,0 a 3,0. V obou příkladech platí, že pokud používáte Azure Functions 1,0, jsou dostupná nastavení stejná, ale část "durableTask" v host.jsby měla být v kořenovém adresáři host.jsv konfiguraci, a ne jako pole v části "rozšíření".

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2. x

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
      "useLegacyPartitionManagement": true,
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
    "useAppLease": true,
    "useGracefulShutdown": false
  }
 }
}

```

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "extensions": {
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
}
```

Názvy centra úloh musí začínat písmenem a obsahovat jenom písmena a číslice. Pokud není zadaný, použije se výchozí název centra úloh pro aplikaci Function App **DurableFunctionsHub**. Další informace najdete v tématu [centra úloh](../articles/azure-functions/durable/durable-functions-task-hubs.md).

|Vlastnost  |Výchozí | Description |
|---------|---------|----------|
|hubName|DurableFunctionsHub|Alternativní názvy [centra úloh](../articles/azure-functions/durable/durable-functions-task-hubs.md) se dají použít k izolaci více Durable Functionsch aplikací od sebe, i když používají stejný back-end úložiště.|
|controlQueueBatchSize|32|Počet zpráv, které mají být vyžádané z fronty ovládacích prvků v čase.|
|controlQueueBufferThreshold| **Plán spotřeby**: 32 <br> **Plán pro vyhrazený/Premium**: 256 |Počet zpráv fronty řízení, které mohou být uloženy do vyrovnávací paměti v paměti v okamžiku, kdy bude dispečer čekat před vyřazením dalších zpráv do fronty.|
|partitionCount |4|Počet oddílů pro frontu řízení. Může být kladné celé číslo mezi 1 a 16.|
|controlQueueVisibilityTimeout |5 minut|Časový limit viditelnosti vyřazení zpráv fronty řízení ve frontě.|
|workItemQueueVisibilityTimeout |5 minut|Časový limit viditelnosti zpráv fronty pracovních položek ve frontě|
|maxConcurrentActivityFunctions | **Plán spotřeby**: 10 <br> **Plán vyhrazený pro Premium**: 10x počet procesorů v aktuálním počítači|Maximální počet funkcí aktivity, které mohou být zpracovány současně na jedné instanci hostitele.|
|maxConcurrentOrchestratorFunctions | **Plán spotřeby**: 5 <br> **Plán vyhrazený pro Premium**: 10x počet procesorů v aktuálním počítači |Maximální počet funkcí nástroje Orchestrator, které mohou být zpracovány současně na jedné instanci hostitele.|
|maxQueuePollingInterval|30 sekund|Maximální interval dotazování fronty řízení a pracovní položky ve formátu *HH: mm: SS* . Vyšší hodnoty můžou mít za následek vyšší latence při zpracování zpráv. Nižší hodnoty můžou mít za následek vyšší náklady na úložiště kvůli zvýšeným transakcím úložiště.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Název nastavení aplikace, které obsahuje připojovací řetězec Azure Storage, který se používá ke správě základních prostředků Azure Storage.|
|trackingStoreConnectionStringName||Název připojovacího řetězce, který se má použít pro tabulky historie a instance. Pokud není zadaný, `connectionStringName` použije se připojení (odolné 2. x) nebo `azureStorageConnectionStringName` (trvanlivé 1. x).|
|trackingStoreNamePrefix||Předpona, která se má použít pro tabulky historie a instance `trackingStoreConnectionStringName` , je-li zadána. Pokud není nastavená, výchozí hodnota předpony bude `DurableTask` . Pokud `trackingStoreConnectionStringName` parametr není zadán, budou tabulky historie a instance používat `hubName` hodnotu jako předpona a jakékoli nastavení pro `trackingStoreNamePrefix` bude ignorováno.|
|traceInputsAndOutputs |false (nepravda)|Hodnota, která označuje, zda se mají trasovat vstupy a výstupy volání funkcí. Výchozí chování při trasování událostí spuštění funkce je zahrnutí počtu bajtů v serializovaných vstupech a výstupech pro volání funkcí. Toto chování poskytuje minimální informace o tom, co vstupy a výstupy vypadají jako bez bloating protokolů nebo neúmyslného zveřejnění citlivých informací. Nastavení této vlastnosti na hodnotu true způsobí, že funkce protokolování výchozích funkcí zaznamená celý obsah vstupů a výstupů funkcí.|
|logReplayEvents|false (nepravda)|Hodnota, která označuje, zda se mají zapisovat události opětovného přehrání orchestrace Application Insights.|
|eventGridTopicEndpoint ||Adresa URL koncového bodu vlastního tématu Azure Event Grid Je-li tato vlastnost nastavena, jsou do tohoto koncového bodu publikovány události oznámení o životním cyklu orchestrace. Tato vlastnost podporuje řešení nastavení aplikace.|
|eventGridKeySettingName ||Název nastavení aplikace obsahující klíč používaný k ověřování pomocí vlastního tématu Azure Event Grid v `EventGridTopicEndpoint` .|
|eventGridPublishRetryCount|0|Počet pokusů o opakování, pokud se publikování do Event Grid tématu nezdařilo.|
|eventGridPublishRetryInterval|5 minut|Event Grid publikuje interval opakování ve formátu *HH: mm: SS* .|
|eventGridPublishEventTypes||Seznam typů událostí pro publikování Event Grid. Není-li tento parametr zadán, budou publikovány všechny typy událostí. Povolené hodnoty zahrnují `Started` , `Completed` , `Failed` , `Terminated` .|
|useAppLease|true|Když se nastaví na `true` , aplikace budou vyžadovat zapůjčení objektu BLOB na úrovni aplikace před zpracováním zpráv centra úloh. Další informace najdete v dokumentaci [pro zotavení po havárii a geografickou distribuci](../articles/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution.md) . K dispozici od verze v 2.3.0.
|useLegacyPartitionManagement|true|Pokud je nastaveno na `false` , používá algoritmus správy oddílů, který při horizontálním navýšení kapacity omezuje možnost duplikace spouštění funkce.  K dispozici od verze v 2.3.0. Výchozí hodnota se změní na `false` v budoucí verzi.|
|useGracefulShutdown|false (nepravda)|Tisk Povolit bezproblémové vypnutí, aby se snížila pravděpodobnost vypnutí hostitelských pokusů o zpracování funkcí v procesu.|

Mnohé z těchto nastavení jsou pro optimalizaci výkonu. Další informace najdete v tématu [výkon a škálování](../articles/azure-functions/durable/durable-functions-perf-and-scale.md).