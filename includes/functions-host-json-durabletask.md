---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a3af711503445000d9613feb2eec7967442fe538
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736111"
---
Nastavení konfigurace pro [Durable Functions](../articles/azure-functions/durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

Centrum názvy úloh musí začínat písmenem a obsahovat jenom písmena a číslice. Pokud není zadán, výchozí název centra úloh aplikace function App je **DurableFunctionsHub**. Další informace najdete v tématu [úkolů rozbočovače](../articles/azure-functions/durable-functions-task-hubs.md).

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Alternativní [centra úloh](../articles/azure-functions/durable-functions-task-hubs.md) názvy můžete použít k izolaci více aplikací Durable Functions od sebe navzájem, i v případě, že používají stejný back-endu úložiště.|
|ControlQueueBatchSize|32|Počet zpráv o přijetí změn z fronty ovládací prvek v čase.|
|PartitionCount |4|Počet oddílů pro frontu ovládacího prvku. Může být kladné celé číslo od 1 do 16.|
|ControlQueueVisibilityTimeout |5 minut|Časový limit viditelnosti ovládacího prvku vyřazených z fronty zpráv.|
|WorkItemQueueVisibilityTimeout |5 minut|Časový limit viditelnosti zpráv vyřazených z fronty pracovní položku.|
|MaxConcurrentActivityFunctions |10 × počet procesorů na aktuálním počítači|Maximální počet funkce aktivity, které je možné zpracovávat současně na jednom hostiteli instance.|
|MaxConcurrentOrchestratorFunctions |10 × počet procesorů na aktuálním počítači|Maximální počet funkcí nástroje orchestrator, které je možné zpracovávat současně na jednom hostiteli instance.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Název nastavení aplikace, které obsahuje připojovací řetězec služby Azure Storage používá ke správě základní prostředky služby Azure Storage.|
|TraceInputsAndOutputs |false (nepravda)|Hodnota označující, zda se pro sledování vstupů a výstupů volání funkce. Výchozí chování při trasování událostí spuštění funkce se zahrnou počet bajtů v serializovaném vstupy a výstupy pro volání funkce. To poskytuje minimální informace o vstupy a výstupy vypadat bez nadměrnému nárůstu velikosti protokolů nebo neúmyslně odhalují citlivé informace do protokolů. Nastavení této vlastnosti na hodnotu true způsobí, že je výchozí funkce protokolování do protokolu celý obsah vstupy a výstupy funkcí.|
|LogReplayEvents|false (nepravda)|Hodnota určující, jestli se má zapsat události opakování Orchestrace do Application Insights.|
|EventGridTopicEndpoint ||Adresa URL koncového bodu služby Azure Event Grid vlastního tématu. Pokud je tato vlastnost nastavena, Orchestrace životní cyklus oznámení události se publikují do tohoto koncového bodu. Tato vlastnost podporuje překlad nastavení aplikace.|
|EventGridKeySettingName ||Název nastavení aplikace, který obsahuje klíč používaný k ověřování pomocí Azure Event Grid vlastního tématu v `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Počet pokusů o zopakování Pokud publikování do tématu Event gridu se nezdaří.|
|EventGridPublishRetryInterval|5 minut|Interval opakování v publikuje služby Event Grid *hh: mm:* formátu.|

Mnoho z nich je pro optimalizaci výkonu. Další informace najdete v tématu [výkon a škálování](../articles/azure-functions/durable-functions-perf-and-scale.md).