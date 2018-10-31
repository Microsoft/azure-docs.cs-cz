---
title: referenční materiály k Host.JSON pro Azure Functions 1.x
description: Referenční dokumentace pro Azure Functions host.json soubor s modulem runtime verze 1.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: ee82aab37973117b0c1960d8b75a29bfad38b7c7
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252163"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>referenční materiály k Host.JSON pro Azure Functions 1.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Verze 1](functions-host-json-v1.md)
> * [Verze 2](functions-host-json.md)

*Host.json* soubor metadat obsahuje možnosti globální konfigurace, které ovlivňují všechny funkce aplikace function App. Tento článek obsahuje seznam nastavení, které jsou k dispozici pro modul runtime verze 1. Schéma JSON je na http://json.schemastore.org/host.

> [!NOTE]
> Tento článek je určený pro Azure Functions 1.x.  Pro odkaz host.json ve funkcích 2.x, naleznete v tématu [referenční materiály k host.json pro Azure Functions 2.x](functions-host-json.md).

Další možnosti konfigurace aplikace funkce se spravují v vaše [nastavení aplikace](functions-app-settings.md).

Některá nastavení host.json se použijí jenom při spuštění místně v [local.settings.json](functions-run-local.md#local-settings-file) souboru.

## <a name="sample-hostjson-file"></a>Ukázkový soubor host.json

Následující ukázka *host.json* soubory mají všechny zadané možnosti.


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

Následující části tohoto článku popisují jednotlivé vlastnosti nejvyšší úrovně. Všechny jsou volitelné, pokud není uvedeno jinak.

## <a name="aggregator"></a>Agregátor

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>centra událostí

Nastavení konfigurace pro [centra Event aktivačními událostmi a vazbami](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Seznam funkcí, které spustí úlohu hostitele. Prázdné pole znamená, že spuštění všech funkcí. Určený k použití pouze tehdy, když [spuštěná místně](functions-run-local.md). V aplikace function App v Azure, by měl místo toho podle kroků v [zakázání funkcí ve službě Azure Functions](disable-function.md) zakázat konkrétní funkce místo použití tohoto nastavení.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Určuje dobu trvání časového limitu pro všechny funkce. V plánu Consumption bez serveru platný rozsah je od 1 sekundy do 10 minut a výchozí hodnota je 5 minut. V plánu služby App Service neexistuje žádné omezení celkové a výchozí hodnota závisí na verzi modulu runtime.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Nastavení konfigurace pro [monitorování stavu hostitelů](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

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
|povoleno|true (pravda)|Určuje, zda je povolena funkce. | 
|healthCheckInterval|10 sekund|Časový interval mezi stavu na pozadí pravidelně kontroluje. | 
|healthCheckWindow|2 minut|Použít ve spojení s klouzavého časového období `healthCheckThreshold` nastavení.| 
|healthCheckThreshold|6|Maximální počet pokusů o kontrolu stavu může selhat, předtím, než je zahájeno recyklace hostitele.| 
|counterThreshold|0,80|Prahová hodnota, na které čítače výkonu se budou považovat za není v pořádku.| 

## <a name="http"></a>http

Nastavení konfigurace pro [http triggerů a vazeb](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Verzi 1.x pouze.*

Jedinečné ID pro úlohu hostitele. Mohou být malé písmeno identifikátor GUID s pomlčkami odebrány. Vyžadováno při místním spuštění. Při spuštění v Azure, doporučujeme nastavit není hodnotou ID. ID je v Azure automaticky vygeneruje při `id` je vynechán. 

Pokud sdílíte mezi více aplikací funkcí účet úložiště, ujistěte se, že každá aplikace function app má jinou `id`. Můžete vynechat `id` vlastnost nebo ručně nastavit vaší aplikace funkcí `id` na jinou hodnotu. Trigger časovače používá úložiště zámek k zajištění, že bude existovat pouze jedna instance časovač při aplikaci function app horizontálně navýší kapacitu na několik instancí. Pokud dvě aplikace function App sdílet stejný `id` a každý používá aktivaci časovačem, bude spuštěna pouze jedna časovače.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Protokolovací nástroj

Ovládací prvky filtrování protokolů autorem [objektu ILogger](functions-monitoring.md#write-logs-in-c-functions) nebo [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

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
|defaultLevel|Informace|Pro všechny kategorie, není zadáno v `categoryLevels` pole, odeslat protokoly na této úrovni a novější do služby Application Insights.| 
|categoryLevels|neuvedeno|Pole kategorií, které určuje úroveň protokolu minimální k odeslání do Application Insights pro každou kategorii. Kategorie tady zadané, řídí všechny kategorie, které začínají stejnou hodnotu a delší hodnoty přednost. V předchozím příkladu *host.json* soubor, všechny kategorie, které začínají řetězcem "Host.Aggregator" protokolu v `Information` úroveň. Všechny kategorie, které začínají řetězcem "Hostitel", jako je například "Host.Executor" protokolu `Error` úroveň.| 

## <a name="queues"></a>fronty

Nastavení konfigurace pro [úložiště fronty aktivačními událostmi a vazbami](functions-bindings-storage-queue.md).

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>služby Service Bus

Nastavení konfigurace pro [aktivační události služby Service Bus a vazby](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Nastavení konfigurace pro chování zámku typu Singleton. Další informace najdete v tématu [problém Githubu o podpoře typu singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

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
|lockPeriod|00:00:15|Dobu, po kterou úroveň zámků funkce pořízeny pro. Zámky automatického obnovení.| 
|listenerLockPeriod|00:01:00|Období, ve kterém zamkne naslouchací proces pořízeny pro.| 
|listenerLockRecoveryPollingInterval|00:01:00|Časový interval použít pro obnovení zámku naslouchací proces, pokud při spuštění nebylo možné získat zámek naslouchací proces.| 
|lockAcquisitionTimeout|00:01:00|Maximální množství času se modul runtime pokusí se získat zámek.| 
|lockAcquisitionPollingInterval|neuvedeno|Interval mezi pokusy o získání zámku.| 

## <a name="tracing"></a>trasování

*Verzi 1.x*

Nastavení konfigurace, které vytvoříte pomocí `TraceWriter` objektu. Zobrazit [jazyka C# protokolování](functions-reference-csharp.md#logging) a [Node.js protokolování](functions-reference-node.md#writing-trace-output-to-the-console).

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
|consoleLevel|informace|Úroveň trasování pro protokolování konzoly. Možnosti jsou: `off`, `error`, `warning`, `info`, a `verbose`.|
|fileLoggingMode|debugOnly|Úroveň trasování pro protokolování do souboru. Možnosti jsou `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Sada [sdílených adresářů kód](functions-reference-csharp.md#watched-directories) , který je potřeba sledovat změny.  Zajišťuje, že při změně kódu v těchto adresářích, změny se prodlouží vašich funkcí.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak aktualizovat soubor host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Podívejte se globální nastavení proměnné prostředí](functions-app-settings.md)
