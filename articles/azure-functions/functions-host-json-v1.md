---
title: host.jsna referenci pro Azure Functions 1. x
description: Referenční dokumentace pro Azure Functions host.jsv souboru s modulem Runtime v1.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 588ab6723015f34d15e4a46ec4f7324302b13b81
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832819"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>host.jsna referenci pro Azure Functions 1. x

> [!div class="op_single_selector" title1="Vyberte verzi Azure Functions runtime, kterou používáte: "]
> * [Verze 1](functions-host-json-v1.md)
> * [Verze 2](functions-host-json.md)

*host.jsv* souboru metadat obsahuje možnosti globální konfigurace, které mají vliv na všechny funkce aplikace Function App. Tento článek obsahuje seznam nastavení, která jsou k dispozici pro modul runtime v1. Schéma JSON je na http://json.schemastore.org/host .

> [!NOTE]
> Tento článek je určen pro Azure Functions 1. x.  Odkaz na host.jsve funkcích 2. x a novějších naleznete v tématu [host.json reference for Azure Functions 2. x](functions-host-json.md).

Další možnosti konfigurace aplikace Function App jsou spravované v [nastavení aplikace](functions-app-settings.md).

Některá host.jsv nastavení se používají jenom v případě, že se spouští místně v [local.settings.js](functions-run-local.md#local-settings-file) souboru.

## <a name="sample-hostjson-file"></a>Ukázka host.jssouboru

Následující vzor *host.js* souborů má všechny možné možnosti.


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
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
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
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
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

## <a name="documentdb"></a>DocumentDB

Nastavení konfigurace [aktivační události Azure Cosmos DB a vazeb](functions-bindings-cosmosdb.md).

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|GatewayMode|brána|Režim připojení, který funkce používá při připojování ke službě Azure Cosmos DB. Možnosti jsou `Direct` a `Gateway`|
|Protokol|Https|Protokol připojení, který funkce používá při připojení ke službě Azure Cosmos DB.  Přečtěte si [zde pro vysvětlení obou režimů](../cosmos-db/performance-tips.md#networking) .|
|leasePrefix|neuvedeno|Předpona zapůjčení pro použití ve všech funkcích aplikace|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Nastavení konfigurace [aktivačních událostí a vazeb centra událostí](functions-bindings-event-hubs-trigger.md#functions-1x)

## <a name="functions"></a> – funkce

Seznam funkcí, které hostitel úlohy spouští. Prázdné pole znamená spuštění všech funkcí. Určeno pro použití pouze při [místním spuštění](functions-run-local.md). V aplikacích Function App v Azure byste měli místo použití tohoto nastavení zakázat konkrétní funkce pomocí postupu v [Azure Functions](disable-function.md) .

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Označuje dobu trvání časového limitu pro všechny funkce. V plánu spotřeby bez serveru je platný rozsah od 1 sekundy do 10 minut a výchozí hodnota je 5 minut. V plánu App Service neexistuje žádný celkový limit a výchozí hodnota je _null_, což znamená, že nevypršel časový limit.

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

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false (nepravda)|Když je toto nastavení povolené, bude v kanálu zpracování požadavků pravidelně kontrolovat čítače výkonu systému, jako jsou připojení/vlákna, procesy/paměti/CPU/a, pokud některý z těchto čítačů překročí vestavěnou vysokou prahovou hodnotu (80%), požadavky se odmítnou se 429 "příliš zaneprázdněnou", dokud se čítače nevrátí na normální úrovně.|
|maxConcurrentRequests|bez vazby ( `-1` )|Maximální počet funkcí HTTP, které se spustí paralelně. To vám umožňuje řídit souběžnost, což pomáhá spravovat využití prostředků. Můžete mít například funkci HTTP, která používá velké množství systémových prostředků (paměť/procesor/sokety), což způsobuje problémy, pokud je souběžnost příliš vysoká. Nebo může být funkce, která vytváří odchozí požadavky na službu třetí strany, a tyto hovory musí být omezené na míru. V těchto případech vám může pomáhat použití omezení.|
|maxOutstandingRequests|bez vazby ( `-1` )|Maximální počet nezpracovaných požadavků, které jsou v daném okamžiku uchovávány. Tento limit zahrnuje požadavky, které jsou ve frontě, ale nezačaly běžet, a také jakékoli probíhající provádění. Všechny příchozí žádosti přes toto omezení se odmítnou s 429 "příliš zaneprázdněnou" odezvou. Umožňuje volajícím využívat strategie opakování na základě času a také vám pomůže řídit maximální latenci žádostí. Tato možnost řídí služby Řízení front zpráv, ke kterým dochází v cestě spuštění hostitele skriptu. Další fronty, například fronta žádostí ASP.NET, budou stále platit a nebudou ovlivněny tímto nastavením.|
|routePrefix|api|Předpona trasy, která se vztahuje na všechny trasy. K odebrání výchozí předpony použijte prázdný řetězec. |

## <a name="id"></a>id

Jedinečné ID pro hostitele úlohy Může to být malá GUID s odebranými pomlčkami. Vyžaduje se při místním spuštění. Při spuštění v Azure doporučujeme nenastavit hodnotu ID. ID se generuje automaticky v Azure, když `id` se vynechá. 

Pokud sdílíte účet úložiště napříč více aplikacemi Function App, ujistěte se, že každá aplikace Function App má jinou `id` . Můžete vynechat `id` vlastnost nebo ručně nastavit každou aplikaci funkcí `id` na jinou hodnotu. Aktivační událost časovače používá zámek úložiště k tomu, aby se zajistilo, že pokud se aplikace funkcí škáluje na více instancí, bude to mít jenom jednu instanci časovače. Pokud dvě aplikace Function App sdílí stejný `id` a každý z nich používá aktivační událost časovače, spustí se jenom jeden časovač.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Nástroj

Řídí filtrování protokolů zapsaných objektem [ILogger](functions-dotnet-class-library.md#ilogger) nebo [kontextem. log](functions-reference-node.md#contextlog-method).

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
|defaultLevel|Informace|Pro jakékoli kategorie, které nejsou určené v `categoryLevels` poli, odešlete protokoly na této úrovni a výše do Application Insights.| 
|categoryLevels|neuvedeno|Pole kategorií, které určuje minimální úroveň protokolu, která se má odeslat Application Insights pro každou kategorii. Zde uvedená kategorie řídí všechny kategorie, které začínají stejnou hodnotou a mají přednost před delšími hodnotami. V předchozí ukázce *host.jsv* souboru všechny kategorie, které začínají na "host. agregátor" na úrovni protokolu `Information` . Všechny ostatní kategorie, které začínají na "hostitel", například Host.Executor, na `Error` úrovni protokolu.| 

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
|batchSize|16|Počet zpráv ve frontě, které funkce runtime Functions načítá současně a procesy paralelně. Když se zpracovávané číslo vrátí do `newBatchThreshold` , modul runtime získá další dávku a začne zpracovávat tyto zprávy. Proto je navíc maximální počet souběžných zpráv zpracovávaných na funkci `batchSize` plus `newBatchThreshold` . Toto omezení se vztahuje odděleně na jednotlivé funkce aktivované frontou. <br><br>Pokud se chcete vyhnout paralelnímu provádění zpráv přijatých v jedné frontě, můžete nastavit `batchSize` na hodnotu 1. Toto nastavení však eliminuje souběžnost, pokud vaše aplikace Function App běží na jednom virtuálním počítači. Pokud se aplikace funkcí škáluje na více virtuálních počítačů, každý virtuální počítač může spustit jednu instanci každé funkce aktivované frontou.<br><br>Maximální `batchSize` hodnota je 32. | 
|maxDequeueCount|5|Počet pokusů o zpracování zprávy před jejich přesunutím do nepoškozené fronty.| 
|newBatchThreshold|batchSize/2|Pokaždé, když se počet zpracovávaných zpráv souběžně vrátí k tomuto číslu, modul runtime načte další dávku.| 

## <a name="sendgrid"></a>SendGrid

Nastavení konfigurace pro [výstupní vazbu SendGrind](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
}    
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|Výsledkem|neuvedeno|E-mailová adresa odesílatele napříč všemi funkcemi.| 

## <a name="servicebus"></a>serviceBus

Nastavení konfigurace pro [aktivační události Service Bus a vazby](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|maxConcurrentCalls|16|Maximální počet souběžných volání zpětného volání, které by mělo iniciovat čerpadlo zpráv. Ve výchozím nastavení aplikace runtime Functions zpracovává více zpráv souběžně. Pokud chcete modul runtime nasměrovat tak, aby zpracovával jenom jednu frontu nebo zprávu o tématu, nastavte `maxConcurrentCalls` na hodnotu 1. | 
|prefetchCount|neuvedeno|Výchozí PrefetchCount, které bude používat základní MessageReceiver.| 
|autoRenewTimeout|00:05:00|Maximální doba, během které bude zámek zprávy obnoven automaticky.|
|Zobrazovat|true|V případě hodnoty true bude aktivační událost po úspěšném provedení operace dokončit zpracování zprávy automaticky. Je-li hodnota false, je odpovědností funkce k dokončení zprávy před vrácením.|

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

Nastavení konfigurace pro protokoly, které vytvoříte pomocí `TraceWriter` objektu. Další informace najdete v tématu [protokolování C#].

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
|consoleLevel|příjemce|Úroveň trasování pro protokolování konzoly. Možnosti jsou: `off` , `error` , `warning` , `info` a `verbose` .|
|fileLoggingMode|debugOnly|Úroveň trasování pro protokolování souborů. Možnosti jsou `never` , `always` , `debugOnly` .| 

## <a name="watchdirectories"></a>watchDirectories

Sada [sdílených adresářů kódu](functions-reference-csharp.md#watched-directories) , které by měly být monitorovány pro změny.  Zajistí, že při změně kódu v těchto adresářích jsou změny převzaty funkcemi.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o tom, jak aktualizovat host.jsv souboru](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Zobrazit globální nastavení v proměnných prostředí](functions-app-settings.md)
