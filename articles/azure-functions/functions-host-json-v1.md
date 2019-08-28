---
title: Reference Host. JSON pro Azure Functions 1. x
description: Referenční dokumentace k souboru Azure Functions Host. JSON s modulem Runtime v1
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: c169d9cc774a2c6264ba1520240005f13ba9d2da
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096456"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Reference Host. JSON pro Azure Functions 1. x

> [!div class="op_single_selector" title1="Vyberte verzi Azure Functions runtime, kterou používáte: "]
> * [Verze 1](functions-host-json-v1.md)
> * [Verze 2](functions-host-json.md)

Soubor metadat *Host. JSON* obsahuje možnosti globální konfigurace, které mají vliv na všechny funkce aplikace Function App. Tento článek obsahuje seznam nastavení, která jsou k dispozici pro modul runtime v1. Schéma JSON je na http://json.schemastore.org/host.

> [!NOTE]
> Tento článek je určený pro Azure Functions 1.x.  Odkaz na Host. JSON ve funkcích 2. x najdete v tématu [reference Host. JSON pro Azure Functions 2. x](functions-host-json.md).

Další možnosti konfigurace aplikace Function App jsou spravované v [nastavení aplikace](functions-app-settings.md).

Některá nastavení Host. JSON se používají jenom v případě, že se spouští místně v souboru [Local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="sample-hostjson-file"></a>Ukázkový soubor host. JSON

Následující ukázkové soubory *Host. JSON* mají uvedené všechny možné možnosti.


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

Následující části tohoto článku vysvětlují jednotlivé vlastnosti nejvyšší úrovně. Všechny jsou volitelné, pokud není uvedeno jinak.

## <a name="aggregator"></a>agregovan

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>centra událostí

Nastavení konfigurace [aktivačních událostí a vazeb centra událostí](functions-bindings-event-hubs.md)

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>– funkce

Seznam funkcí, které hostitel úlohy spouští. Prázdné pole znamená spuštění všech funkcí. Určeno pro použití pouze při [místním spuštění](functions-run-local.md). V aplikacích Function App v Azure byste měli místo použití tohoto nastavení zakázat konkrétní funkce pomocí postupu v [Azure Functions](disable-function.md) .

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Označuje dobu trvání časového limitu pro všechny funkce. V plánu spotřeby bez serveru je platný rozsah od 1 sekundy do 10 minut a výchozí hodnota je 5 minut. V plánu App Service neexistuje žádný celkový limit a výchozí hodnota závisí na verzi modulu runtime.

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
|enabled|true|Určuje, jestli je funkce povolená. | 
|healthCheckInterval|10 sekund|Časový interval mezi pravidelnými kontrolami stavu pozadí. | 
|healthCheckWindow|2 minuty|Posuvné časové okno používané ve spojení s `healthCheckThreshold` nastavením.| 
|healthCheckThreshold|6|Maximální počet neúspěšných kontrol stavu před zahájením recyklace hostitele.| 
|counterThreshold|0,80|Prahová hodnota, při které bude čítač výkonu považován za špatný.| 

## <a name="http"></a>http

Nastavení konfigurace [aktivačních událostí a vazeb HTTP](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Pouze verze 1. x.*

Jedinečné ID pro hostitele úlohy Může to být malá GUID s odebranými pomlčkami. Vyžaduje se při místním spuštění. Při spuštění v Azure doporučujeme nenastavit hodnotu ID. ID se generuje automaticky v Azure, když `id` se vynechá. 

Pokud sdílíte účet úložiště napříč více aplikacemi Function App, ujistěte se, že každá aplikace Function App má `id`jinou. Můžete vynechat `id` vlastnost nebo ručně nastavit každou `id` aplikaci funkcí na jinou hodnotu. Aktivační událost časovače používá zámek úložiště k tomu, aby se zajistilo, že pokud se aplikace funkcí škáluje na více instancí, bude to mít jenom jednu instanci časovače. Pokud dvě aplikace Function App sdílí stejný `id` a každý z nich používá aktivační událost časovače, spustí se jenom jeden časovač.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Nástroj

Řídí filtrování protokolů zapsaných [objektem ILogger](functions-monitoring.md#write-logs-in-c-functions) nebo [kontextem. log](functions-monitoring.md#write-logs-in-javascript-functions).

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
|categoryFilter|neuvedeno|Určuje filtrování podle kategorie.| 
|defaultLevel|Information|Pro jakékoli kategorie, které nejsou určené `categoryLevels` v poli, odešlete protokoly na této úrovni a výše do Application Insights.| 
|categoryLevels|neuvedeno|Pole kategorií, které určuje minimální úroveň protokolu, která se má odeslat Application Insights pro každou kategorii. Zde uvedená kategorie řídí všechny kategorie, které začínají stejnou hodnotou a mají přednost před delšími hodnotami. V předchozím ukázkovém souboru *Host. JSON* všechny kategorie, které začínají na "host. agregátor" na `Information` úrovni protokolu. Všechny ostatní kategorie, které začínají na "hostitel", jako je například Host. prováděcí modul, `Error` úroveň protokolování.| 

## <a name="queues"></a>vytvořil

Nastavení konfigurace [aktivačních událostí a vazeb fronty úložiště](functions-bindings-storage-queue.md)

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|maxPollingInterval|60000|Maximální interval v milisekundách mezi dotazy na frontu.| 
|visibilityTimeout|0|Časový interval mezi opakovanými pokusy při zpracování zprávy se nezdařil.| 
|batchSize|16|Počet zpráv ve frontě, které funkce runtime Functions načítá současně a procesy paralelně. Když se zpracovávané číslo vrátí do `newBatchThreshold`, modul runtime získá další dávku a začne zpracovávat tyto zprávy. Proto je `batchSize` navíc maximální počet souběžných zpráv zpracovávaných na funkci plus `newBatchThreshold`. Toto omezení se vztahuje odděleně na jednotlivé funkce aktivované frontou. <br><br>Pokud se chcete vyhnout paralelnímu provádění zpráv přijatých v jedné frontě, můžete nastavit na `batchSize` hodnotu 1. Toto nastavení však eliminuje souběžnost, pokud vaše aplikace Function App běží na jednom virtuálním počítači. Pokud se aplikace funkcí škáluje na více virtuálních počítačů, každý virtuální počítač může spustit jednu instanci každé funkce aktivované frontou.<br><br>Maximální `batchSize` hodnota je 32. | 
|maxDequeueCount|5|Počet pokusů o zpracování zprávy před jejich přesunutím do nepoškozené fronty.| 
|newBatchThreshold|batchSize/2|Pokaždé, když se počet zpracovávaných zpráv souběžně vrátí k tomuto číslu, modul runtime načte další dávku.| 

## <a name="servicebus"></a>serviceBus

Nastavení konfigurace pro [aktivační události Service Bus a vazby](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|maxConcurrentCalls|16|Maximální počet souběžných volání zpětného volání, které by mělo zahájit pumpu zpráv. Ve výchozím nastavení modul runtime služby Functions zpracovávat více zpráv souběžně. Chcete-li řídit modul runtime najednou zpracovat pouze jedné frontě nebo tématu zprávy, nastavte `maxConcurrentCalls` na hodnotu 1. | 
|prefetchCount|neuvedeno|Výchozí PrefetchCount, který se použije základní MessageReceiver.| 
|autoRenewTimeout|00:05:00|Maximální doba, ve kterém se automatické obnovení zámku zprávy.| 

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

## <a name="tracing"></a>probíhá

*Verze 1. x*

Nastavení konfigurace pro protokoly, které vytvoříte pomocí `TraceWriter` objektu. Viz [ C# protokolování](functions-reference-csharp.md#logging) a [protokolování Node. js](functions-reference-node.md#writing-trace-output-to-the-console).

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
|consoleLevel|info|Úroveň trasování pro protokolování konzoly. Možnosti jsou: `off`, `error`, `warning`, `info`a .`verbose`|
|fileLoggingMode|debugOnly|Úroveň trasování pro protokolování souborů. Možnosti jsou `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Sada [sdílených adresářů kódu](functions-reference-csharp.md#watched-directories) , které by měly být monitorovány pro změny.  Zajistí, že při změně kódu v těchto adresářích jsou změny převzaty funkcemi.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Naučte se aktualizovat soubor host. JSON.](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Zobrazit globální nastavení v proměnných prostředí](functions-app-settings.md)
