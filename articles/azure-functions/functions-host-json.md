---
title: Host.JSON odkazu pro Azure Functions
description: Referenční dokumentace pro soubor host.json Azure Functions.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/12/2018
ms.author: tdykstra
ms.openlocfilehash: d1dec6f2da4f6fcbeb38585fc6a1cfcd9d622c4a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="hostjson-reference-for-azure-functions"></a>Host.JSON odkazu pro Azure Functions

*Host.json* soubor metadat obsahuje možnosti globální konfigurace, které ovlivňují všechny funkce pro funkce aplikace. Tento článek obsahuje seznam nastavení, které jsou k dispozici. Schéma JSON je v http://json.schemastore.org/host.

Existují další možnosti globální konfigurace v [nastavení aplikace](functions-app-settings.md) a v [local.settings.json](functions-run-local.md#local-settings-file) souboru.

## <a name="sample-hostjson-file"></a>Ukázkový soubor host.json

Následující ukázka *host.json* soubor obsahuje všechny možné možností.

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

Následující části tohoto článku popisují každou vlastnost nejvyšší úrovně. Všechny jsou volitelné, pokud není uvedeno jinak.

## <a name="aggregator"></a>Agregátor

Určuje, kolik volání funkce agregovat při [výpočet metriky pro službu Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Vlastnost |Výchozí  | Popis |
|---------|---------|---------| 
|batchSize|1000|Maximální počet požadavků, které k agregaci.| 
|flushTimeout|00:00:30|Maximální doba období k agregaci.| 

Volání funkce jsou agregovat při první dva omezuje se dosáhne.

## <a name="applicationinsights"></a>applicationInsights

Ovládací prvky [vzorkování funkce ve službě Application Insights](functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|Hodnotu IsEnabled|true (pravda)|Povolí nebo zakáže vzorkování.| 
|maxTelemetryItemsPerSecond|5|Prahová hodnota, na které vzorkování začne.| 

## <a name="durabletask"></a>durableTask

Nastavení konfigurace pro [trvanlivý funkce](durable-functions-overview.md).

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
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey"
  }
}
```

Úloha rozbočovače názvy musí začínat písmenem a obsahovat jenom písmena a čísla. Pokud není zadáno, je výchozí název rozbočovače úlohy pro funkce aplikace **DurableFunctionsHub**. Další informace najdete v tématu [úloh centra](durable-functions-task-hubs.md).

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternativní [úloh centra](durable-functions-task-hubs.md) názvy lze izolovat více trvanlivý funkce aplikace od sebe navzájem, i když používají stejný back-endu úložiště.|
|ControlQueueBatchSize|32|Počet zpráv načítat z fronty řízení najednou.|
|PartitionCount |4|Počet oddílů pro frontu ovládacího prvku. Může být kladné celé číslo mezi 1 a 16.|
|ControlQueueVisibilityTimeout |5 minut|Časový limit viditelnosti vyjmutou řízení fronty zpráv.|
|WorkItemQueueVisibilityTimeout |5 minut|Časový limit viditelnosti vyjmutou pracovní položka fronty zpráv.|
|MaxConcurrentActivityFunctions |10 x počet procesorů do aktuálního počítače|Maximální počet funkcí aktivity, které mohou být zpracovány současně na jednom hostiteli instanci.|
|MaxConcurrentOrchestratorFunctions |10 x počet procesorů do aktuálního počítače|Maximální počet funkcí aktivity, které mohou být zpracovány současně na jednom hostiteli instanci.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Název nastavení aplikace, který má připojovací řetězec úložiště Azure používat ke správě základní prostředky Azure Storage.|
|TraceInputsAndOutputs |false (nepravda)|Hodnota, která určuje, zda chcete trasovat vstupy a výstupy volání funkce. Výchozí chování při trasování funkce zpracování událostí je zahrnují počet bajtů v serializovaných vstupy a výstupy pro volání funkcí. To poskytuje minimální informace o jak vstupy a výstupy vypadat bez nadměrnému nárůstu velikosti protokolů nebo neúmyslně odhalení citlivých informací do protokolů. Nastavení této vlastnosti na hodnotu true způsobí, že výchozí funkce protokolování do protokolu celý obsah funkce vstupy a výstupy.|
|EventGridTopicEndpoint ||Adresa URL koncového bodu Azure událostí mřížky vlastní tématu. Pokud je tato vlastnost nastavena, orchestration životní cyklus oznámení události publikují k tomuto koncovému bodu.|
|EventGridKeySettingName ||Název nastavení aplikace, který obsahuje klíč používaný pro ověřování s vlastní téma Azure událostí mřížky v `EventGridTopicEndpoint`.

Mnoho z těchto je pro optimalizaci výkonu. Další informace najdete v tématu [výkonu a možností škálování](durable-functions-perf-and-scale.md).

## <a name="eventhub"></a>Centrum EventHub

Nastavení konfigurace pro [centra událostí triggerů a vazeb](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Seznam funkcí, které budou spouštět úlohy hostitele. Prázdné pole znamená spustit všechny funkce. Určený k použití pouze tehdy, když [spuštěn místně](functions-run-local.md). V aplikacích pro funkce, použijte *function.json* `disabled` vlastnost spíše než tuto vlastnost v *host.json*.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Určuje dobu trvání časového limitu pro všechny funkce. V rámci plánů spotřebu platný rozsah je od 1 sekundy do 10 minut a výchozí hodnota je 5 minut. V plánech služby App Service není omezen a výchozí hodnota je null, což značí bez časového limitu.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Nastavení konfigurace pro [monitorování stavu hostitele](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|povoleno|true (pravda)|Jestli je funkce zapnutá. | 
|healthCheckInterval|10 sekund|Kontroluje, časový interval mezi stavy pravidelných pozadí. | 
|healthCheckWindow|2 minuty|Posuvné okno čas používá ve spojení s `healthCheckThreshold` nastavení.| 
|healthCheckThreshold|6|Maximální počet kontrolou stavu může selhat, než je zahájeno recyklaci hostitele.| 
|counterThreshold|0,80|Prahová hodnota, na které čítač výkonu, který se bude zvažovat není v pořádku.| 

## <a name="http"></a>http

Nastavení konfigurace pro [http triggerů a vazeb](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

Jedinečné ID pro úlohu hostitele. Mohou být malé písmeno GUID s pomlčkami odebrány. Při místním spuštění vyžaduje. Při spuštění v Azure Functions, je-li automaticky vygeneruje ID `id` je vynechán.

Pokud účet úložiště můžete sdílet mezi více aplikacemi funkce, ujistěte se, že každá funkce aplikace má jiné `id`. Můžete vynechat `id` vlastnost nebo ručně nastavit každé funkce aplikace `id` na jinou hodnotu. Aktivační událost časovače používá úložiště zámek k zajištění, že bude pouze jedna instance časovače při aplikaci funkce horizontálně navýší kapacitu na více instancí. Pokud dvě funkce aplikace sdílet stejný `id` a každá používá aktivaci časovačem, bude spuštěna pouze jedna časovače.


```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Protokoly

Ovládací prvky filtrování podle zapisují protokoly [objekt objektu ILogger](functions-monitoring.md#write-logs-in-c-functions) nebo [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|categoryFilter|neuvedeno|Určuje filtrování podle kategorie| 
|defaultLevel|Informace|Pro všechny kategorie, nebyly zadány v `categoryLevels` pole, odeslání protokolů na této úrovni a vyšší Application Insights.| 
|categoryLevels|neuvedeno|Pole kategorií, které určuje úroveň minimální protokolu odeslat do služby Application Insights pro každou kategorii. Kategorie zde zadané řídí všechny kategorie, které začínají stejnou hodnotu a mají přednost před delší hodnoty. V předchozím příkladu *host.json* souboru, všechny kategorie, které začínají "Host.Aggregator" protokolu v `Information` úroveň. Přihlaste se na všech ostatních kategorií, které začínají "Hostitel", jako je například "Host.Executor" `Error` úroveň.| 

## <a name="queues"></a>Fronty

Nastavení konfigurace pro [úložiště fronty triggerů a vazeb](functions-bindings-storage-queue.md).

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>Sběrnice

Nastavení konfigurace pro [Service Bus triggerů a vazeb](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Nastavení konfigurace pro jednotlivý prvek uzamčení chování. Další informace najdete v tématu [potíže Githubu o podpoře singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|lockPeriod|00:00:15|Dobu, po kterou jsou funkce úrovni zámky prováděné na. Zámky automatického obnovení.| 
|listenerLockPeriod|00:01:00|Období, které se provádějí zámky naslouchací proces pro.| 
|listenerLockRecoveryPollingInterval|00:01:00|Časový interval pro naslouchací proces obnovení zámku použit v případě, že při spuštění se nepodařilo získat zámek naslouchací proces.| 
|lockAcquisitionTimeout|00:01:00|Maximální množství času modulu runtime se pokusí získat zámek.| 
|lockAcquisitionPollingInterval|neuvedeno|Interval mezi pokusy o získání zámku.| 

## <a name="tracing"></a>trasování

Nastavení konfigurace pro protokoly, které vytvoříte pomocí `TraceWriter` objektu. V tématu [C# protokolování](functions-reference-csharp.md#logging) a [Node.js protokolování](functions-reference-node.md#writing-trace-output-to-the-console). 

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|consoleLevel|Informace o|Úroveň trasování pro protokolování konzoly. Možnosti jsou: `off`, `error`, `warning`, `info`, a `verbose`.|
|fileLoggingMode|debugOnly|Úroveň trasování pro protokolování do souboru. Možnosti jsou `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Sadu [sdíleného adresáře kód](functions-reference-csharp.md#watched-directories) , je nutné sledovat změny.  Zajišťuje, že při změně kódu v tyto adresáře změny jsou zachyceny pomocí funkcí.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak aktualizovat soubor host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Najdete v části globální nastavení v seznamu proměnných prostředí](functions-app-settings.md)
