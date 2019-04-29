---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710522"
---
Nastavení konfigurace pro [Durable Functions](../articles/azure-functions/durable-functions-overview.md).

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
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

Centrum názvy úloh musí začínat písmenem a obsahovat jenom písmena a číslice. Pokud není zadán, výchozí název centra úloh aplikace function App je **DurableFunctionsHub**. Další informace najdete v tématu [úkolů rozbočovače](../articles/azure-functions/durable-functions-task-hubs.md).

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternativní [centra úloh](../articles/azure-functions/durable-functions-task-hubs.md) názvy můžete použít k izolaci více aplikací Durable Functions od sebe navzájem, i v případě, že používají stejný back-endu úložiště.|
|controlQueueBatchSize|32|Počet zpráv o přijetí změn z fronty ovládací prvek v čase.|
|partitionCount |4|Počet oddílů pro frontu ovládacího prvku. Může být kladné celé číslo od 1 do 16.|
|controlQueueVisibilityTimeout |5 minut|Časový limit viditelnosti ovládacího prvku vyřazených z fronty zpráv.|
|workItemQueueVisibilityTimeout |5 minut|Časový limit viditelnosti zpráv vyřazených z fronty pracovní položku.|
|maxConcurrentActivityFunctions |10 × počet procesorů na aktuálním počítači|Maximální počet funkce aktivity, které je možné zpracovávat současně na jednom hostiteli instance.|
|maxConcurrentOrchestratorFunctions |10 × počet procesorů na aktuálním počítači|Maximální počet funkcí nástroje orchestrator, které je možné zpracovávat současně na jednom hostiteli instance.|
|maxQueuePollingInterval|30 sekund|Maximální kontrolu a pracovní položky fronty interval dotazování v *hh: mm:* formátu. Vyšší hodnoty může způsobit zpracování latence vyšší zpráv. Nižší hodnoty může způsobit vyšší náklady na úložiště z důvodu větší úložiště transakce.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Název nastavení aplikace, které obsahuje připojovací řetězec služby Azure Storage používá ke správě základní prostředky služby Azure Storage.|
|trackingStoreConnectionStringName||Název připojovacího řetězce pro tabulky historie a instancí. Pokud není zadán, `azureStorageConnectionStringName` slouží k připojení.|
|trackingStoreNamePrefix||Předpona, kterou chcete použít pro historie a instance tabulky, kdy `trackingStoreConnectionStringName` je zadán. Pokud není nastavený, výchozí hodnota předpona bude `DurableTask`. Pokud `trackingStoreConnectionStringName` není zadán, pak bude používat tabulky historie a instance `hubName` hodnotu jako jejich Předpona a všechna nastavení pro `trackingStoreNamePrefix` budou ignorovány.|
|traceInputsAndOutputs |false (nepravda)|Hodnota označující, zda se pro sledování vstupů a výstupů volání funkce. Výchozí chování při trasování událostí spuštění funkce se zahrnou počet bajtů v serializovaném vstupy a výstupy pro volání funkce. Toto chování poskytuje minimální informace o vstupy a výstupy vypadat bez nadměrnému nárůstu velikosti protokolů nebo neúmyslně odhalují citlivé informace. Nastavení této vlastnosti na hodnotu true způsobí, že je výchozí funkce protokolování do protokolu celý obsah vstupy a výstupy funkcí.|
|LogReplayEvents|false (nepravda)|Hodnota určující, jestli se má zapsat události opakování Orchestrace do Application Insights.|
|eventGridTopicEndpoint ||Adresa URL koncového bodu služby Azure Event Grid vlastního tématu. Pokud je tato vlastnost nastavena, Orchestrace životního cyklu oznámení události se publikují do tohoto koncového bodu. Tato vlastnost podporuje překlad nastavení aplikace.|
|eventGridKeySettingName ||Název nastavení aplikace, který obsahuje klíč používaný k ověřování pomocí Azure Event Grid vlastního tématu v `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Počet pokusů o zopakování Pokud publikování do tématu Event gridu se nezdaří.|
|eventGridPublishRetryInterval|5 minut|Interval opakování v publikuje služby Event Grid *hh: mm:* formátu.|
|eventGridPublishEventTypes||Seznam typů událostí k publikování do služby Event Grid. Pokud není zadán, bude publikován všechny typy událostí. Povolené hodnoty jsou `Started`, `Completed`, `Failed`, `Terminated`.|

Mnohé z těchto nastavení jsou pro optimalizaci výkonu. Další informace najdete v tématu [výkon a škálování](../articles/azure-functions/durable-functions-perf-and-scale.md).