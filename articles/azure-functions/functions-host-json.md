---
title: Reference Host. JSON pro Azure Functions 2. x
description: Referenční dokumentace pro soubor Azure Functions Host. JSON s modulem runtime v2
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 584fb7b97b8342289d7ca2f23b0479eb1169867a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575895"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>Reference Host. JSON pro Azure Functions 2. x  

> [!div class="op_single_selector" title1="Vyberte verzi Azure Functions runtime, kterou používáte: "]
> * [Verze 1](functions-host-json-v1.md)
> * [Verze 2](functions-host-json.md)

Soubor metadat *Host. JSON* obsahuje možnosti globální konfigurace, které mají vliv na všechny funkce aplikace Function App. Tento článek obsahuje seznam nastavení, která jsou k dispozici pro modul runtime v2.  

> [!NOTE]
> Tento článek je určen pro Azure Functions 2. x.  Odkaz na Host. JSON ve funkcích 1. x najdete v [referenčních informacích k host. JSON pro Azure Functions 1. x](functions-host-json-v1.md).

Další možnosti konfigurace aplikace Function App jsou spravované v [nastavení aplikace](functions-app-settings.md).

Některá nastavení Host. JSON se používají jenom v případě, že se spouští místně v souboru [Local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="sample-hostjson-file"></a>Ukázkový soubor host. JSON

Následující ukázkové soubory *Host. JSON* mají uvedené všechny možné možnosti.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
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
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

Následující části tohoto článku vysvětlují jednotlivé vlastnosti nejvyšší úrovně. Všechny jsou volitelné, pokud není uvedeno jinak.

## <a name="aggregator"></a>agregovan

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>ApplicationInsights

Toto nastavení je podřízenou položkou [protokolování](#logging).

Řídí [funkci vzorkování v Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 20
        }
    }
}
```

> [!NOTE]
> Vzorkování protokolu může způsobit, že se některá spuštění v okně monitorování Application Insights neprojeví.

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|isEnabled|true|Povolí nebo zakáže vzorkování.| 
|maxTelemetryItemsPerSecond|20|Prahová hodnota, při které začíná vzorkování.| 
|EnableLiveMetrics |true|Povoluje shromažďování živých metrik.|
|EnableDependencyTracking|true|Povolí sledování závislostí.|
|EnablePerformanceCountersCollection|true|Povolí shromažďování čítačů výkonu Kudu.|

## <a name="cosmosdb"></a>cosmosDb

Nastavení konfigurace najdete v [Cosmos DB triggerech a vazbách](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

Nastavení konfigurace lze nalézt v [vazby pro Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Nastavení konfigurace najdete v [aktivačních událostech a vazbách centra událostí](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>SND

Vlastnost, která vrací objekt, který obsahuje všechna nastavení specifická pro vazbu, například [http](#http) a [eventHub](#eventhub).

## <a name="functions"></a>functions

Seznam funkcí, které hostitel úlohy spouští. Prázdné pole znamená spuštění všech funkcí. Určeno pro použití pouze při [místním spuštění](functions-run-local.md). V aplikacích Function App v Azure byste měli místo použití tohoto nastavení zakázat konkrétní funkce pomocí postupu v [Azure Functions](disable-function.md) .

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Označuje dobu trvání časového limitu pro všechny funkce. Postupuje podle formátu řetězce TimeSpan. V plánu spotřeby bez serveru je platný rozsah od 1 sekundy do 10 minut a výchozí hodnota je 5 minut.  
Ve vyhrazeném (App Service) plánu není celkový limit a výchozí hodnota je 30 minut. Hodnota `-1` označuje neohraničené spuštění.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>Elementu

Nastavení konfigurace pro [Monitor stavu hostitele](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

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
|umožněn|true|Určuje, jestli je funkce povolená. | 
|healthCheckInterval|10 sekund|Časový interval mezi pravidelnými kontrolami stavu pozadí. | 
|healthCheckWindow|2 minuty|Posuvné časové okno používané ve spojení s nastavením `healthCheckThreshold`.| 
|healthCheckThreshold|6|Maximální počet neúspěšných kontrol stavu před zahájením recyklace hostitele.| 
|counterThreshold|0,80|Prahová hodnota, při které bude čítač výkonu považován za špatný.| 

## <a name="http"></a>http

Nastavení konfigurace najdete v [aktivačních událostech http a vazbách](functions-bindings-http-webhook.md#hostjson-settings).

## <a name="logging"></a>Protokolu

Řídí chování protokolování aplikace Function App, včetně Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Definuje, jakou úroveň protokolování souborů je povoleno.  Možnosti jsou `never`, `always``debugOnly`. |
|logLevel|neuvedeno|Objekt, který definuje filtrování kategorií protokolů pro funkce v aplikaci. Verze 2. x se řídí rozložením ASP.NET Core pro filtrování kategorií protokolů. To vám umožní filtrovat protokolování pro konkrétní funkce. Další informace najdete v tématu [filtrování protokolů](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) v dokumentaci k ASP.NET Core. |
|konzola|neuvedeno| Nastavení protokolování [konzoly](#console) . |
|ApplicationInsights|neuvedeno| Nastavení [applicationInsights](#applicationinsights) |

## <a name="console"></a>konzola

Toto nastavení je podřízenou položkou [protokolování](#logging). Řídí protokolování konzoly, pokud není v režimu ladění.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|isEnabled|false (nepravda)|Povolí nebo zakáže protokolování konzoly.| 

## <a name="manageddependency"></a>managedDependency

Spravovaná závislost je funkce, kterou momentálně podporuje jenom funkce založené na PowerShellu. Umožňuje, aby se závislosti automaticky spravovaly službou. Je-li vlastnost `enabled` nastavena na hodnotu `true`, je zpracován soubor `requirements.psd1`. Závislosti se aktualizují při vydání jakýchkoli dílčích verzí. Další informace najdete v tématu [spravovaná závislost](functions-reference-powershell.md#dependency-management) v článku věnovaném prostředí PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>vytvořil

Nastavení konfigurace najdete v [aktivačních událostech a vazbách fronty úložiště](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Nastavení konfigurace najdete v [SendGrid triggerech a vazbách](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Nastavení konfigurace najdete v [Service Bus triggerech a vazbách](functions-bindings-service-bus.md#host-json).

## <a name="singleton"></a>singleton

Nastavení konfigurace pro chování zámku typu singleton. Další informace najdete v tématu [problém GitHubu o podpoře typu Singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

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
|lockPeriod|00:00:15|Období, pro které jsou přijímány zámky na úrovni funkce. Automatické obnovení zámků.| 
|listenerLockPeriod|00:01:00|Období, pro které jsou pořízeny zámky naslouchacího procesu.| 
|listenerLockRecoveryPollingInterval|00:01:00|Časový interval, který se používá pro obnovení zámku naslouchacího procesu, pokud se nepovedlo získat zámek naslouchacího procesu při spuštění.| 
|lockAcquisitionTimeout|00:01:00|Maximální doba, po kterou se modul runtime pokusí získat zámek.| 
|lockAcquisitionPollingInterval|neuvedeno|Interval mezi pokusy o získání zámku.| 

## <a name="version"></a>version

Pro aplikaci Function App, která cílí na modul runtime v2, se vyžaduje `"version": "2.0"` řetězec verze.

## <a name="watchdirectories"></a>watchDirectories

Sada [sdílených adresářů kódu](functions-reference-csharp.md#watched-directories) , které by měly být monitorovány pro změny.  Zajistí, že při změně kódu v těchto adresářích jsou změny převzaty funkcemi.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se aktualizovat soubor host. JSON.](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Zobrazit globální nastavení v proměnných prostředí](functions-app-settings.md)
