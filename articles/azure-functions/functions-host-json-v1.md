---
title: reference host.json pro funkce Azure 1.x
description: Referenční dokumentace pro soubor host.json Azure Functions s runtime v1.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 2b00e2343e0959e07b195e2e98c6719a1893b8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277047"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>reference host.json pro funkce Azure 1.x

> [!div class="op_single_selector" title1="Vyberte verzi runtime Azure Functions, kterou používáte: "]
> * [Verze 1](functions-host-json-v1.md)
> * [Verze 2](functions-host-json.md)

Soubor metadat *host.json* obsahuje možnosti globální konfigurace, které ovlivňují všechny funkce aplikace funkce. Tento článek uvádí nastavení, které jsou k dispozici pro v1 runtime. Schéma JSON je na http://json.schemastore.org/host.

> [!NOTE]
> Tento článek je pro Azure Functions 1.x.  Odkaz na host.json ve funkcích 2.x a novějších najdete [v tématu reference host.json pro Funkce Azure 2.x](functions-host-json.md).

Další možnosti konfigurace aplikace funkce se spravují v [nastavení aplikace](functions-app-settings.md).

Některá nastavení host.json se používají pouze při místním spuštění v souboru [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="sample-hostjson-file"></a>Ukázkový soubor host.json

Následující ukázkové soubory *host.json* mají všechny možné možnosti.


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

Následující části tohoto článku vysvětlují každou vlastnost nejvyšší úrovně. Všechny jsou volitelné, pokud není uvedeno jinak.

## <a name="aggregator"></a>Agregátor

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Nastavení konfigurace pro [aktivační událost Azure Cosmos DB a vazby](functions-bindings-cosmosdb.md).

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
|Režim brány|brána|Režim připojení používaný funkcí při připojování ke službě Azure Cosmos DB. Možnosti `Direct` jsou a`Gateway`|
|Protocol (Protokol)|Protokol Https|Protokol připojení používaný funkcí při připojení ke službě Azure Cosmos DB.  Přečtěte si [zde vysvětlení obou režimů](../cosmos-db/performance-tips.md#networking)|
|zapůjčení předběžné hospo|neuvedeno|Pronajměte předponu, která se použije ve všech funkcích aplikace.|

## <a name="durabletask"></a>odolnýÚkol

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Nastavení konfigurace pro [aktivační události centra událostí a vazby](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a> – funkce

Seznam funkcí, které hostitel úlohy spustí. Prázdné pole znamená spustit všechny funkce. Určeno pro použití pouze při [místním spuštění](functions-run-local.md). Ve funkčních aplikacích v Azure byste místo toho měli postupovat podle pokynů v [části Jak zakázat funkce ve službě Azure Functions](disable-function.md) a zakázat konkrétní funkce, místo abyste používali toto nastavení.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Označuje dobu trvání časového období pro všechny funkce. V plánu spotřeba bez serveru je platný rozsah od 1 sekundy do 10 minut a výchozí hodnota je 5 minut. V plánu služby App Service neexistuje žádné celkové omezení a výchozí hodnota je _null_, což znamená, že žádný časový limit.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Nastavení konfigurace [pro monitor stavu hostitele](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

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
|enabled|true|Určuje, zda je tato funkce povolena. | 
|healthCheckInterval|10 sekund|Časový interval mezi pravidelnými kontrolami stavu na pozadí. | 
|healthCheckWindow|2 minuty|Posuvné časové okno používané ve `healthCheckThreshold` spojení s nastavením.| 
|healthCheckThreshold|6|Maximální počet selhání kontroly stavu před zahájením recyklace hostitele.| 
|counterThreshold|0,80|Prahová hodnota, při které čítač výkonu bude považován za není v pořádku.| 

## <a name="http"></a>HTTP

Nastavení konfigurace pro [aktivační události a vazby http](functions-bindings-http-webhook.md).

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
|dynamicThrottlesEnabled|false (nepravda)|Pokud je toto nastavení povoleno, způsobí, že kanál zpracování požadavků pravidelně kontroluje čítače výkonu systému, jako jsou připojení/vlákna/procesy/paměť/cpu/atd. a pokud některý z těchto čítačů překročí vestavěnou vysokou prahovou hodnotu (80 %), budou požadavky odmítnuty s odpovědí 429 "Too Busy", dokud se čítač vrátí na normální úroveň.|
|maxConcurrentRequests|bez meze`-1`( )|Maximální počet funkcí PROTOKOLU HTTP, které budou spuštěny paralelně. To umožňuje řídit souběžnost, která může pomoci spravovat využití prostředků. Můžete mít například funkci HTTP, která používá velké množství systémových prostředků (paměť/procesor/sokety), takže způsobuje problémy, když je souběžnost příliš vysoká. Nebo můžete mít funkci, která provádí odchozí požadavky na službu třetí strany a tato volání musí být omezena. V těchto případech může pomoci použití škrticí klapky.|
|maxOutstandingRequests|bez meze`-1`( )|Maximální počet nevyřízených požadavků, které jsou v daném okamžiku uchovávány. Toto omezení zahrnuje požadavky, které jsou zařazeny do fronty, ale nebyly spuštěny, stejně jako všechny probíhající spuštění. Všechny příchozí požadavky nad tento limit jsou odmítnuty s odpovědí 429 "Příliš zaneprázdněn". To umožňuje volajícím využívat strategie opakování založené na čase a také vám pomůže řídit maximální latenci požadavků. To to pouze řídí fronty, ke kterému dochází v rámci cesty spuštění hostitele skriptu. Ostatní fronty, například ASP.NET fronty požadavků, budou tímto nastavením stále platné a nebudou ovlivněny.|
|routePrefix|rozhraní api|Předpona trasy, která platí pro všechny trasy. K odebrání výchozí předpony použijte prázdný řetězec. |

## <a name="id"></a>id

Jedinečné ID hostitele úlohy. Může se na pohřešovat písmeny GUID s odstraněnými pomlčkami. Povinné při spuštění místně. Když běžív Azure, doporučujeme nenastavovat hodnotu ID. ID se vAzure vygeneruje automaticky, když `id` je vynecháno. 

Pokud sdílíte účet úložiště mezi aplikacemi s více funkcemi, ujistěte se, že každá aplikace funkcí má jinou `id`. Vlastnost můžete vynechat `id` nebo ručně nastavit jednotlivé aplikace `id` funkcí na jinou hodnotu. Aktivační událost časovače používá zámek úložiště k zajištění, že bude existovat pouze jedna instance časovače, když se aplikace funkce škáluje na více instancí. Pokud dvě aplikace funkcí `id` sdílejí stejné a každá používá aktivační událost časovače, spustí se pouze jeden časovač.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Logger

Řídí filtrování protokolů napsaných [objektem ILogger](functions-monitoring.md#write-logs-in-c-functions) nebo [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

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
|defaultLevel|Informace|Pro všechny kategorie, `categoryLevels` které nejsou zadány v poli, odesílají protokoly na této a vyšší úrovni application insights.| 
|kategorieÚrovně|neuvedeno|Pole kategorií, které určuje minimální úroveň protokolu, která má být odeslána do application insights pro každou kategorii. Kategorie zde určená určuje všechny kategorie, které začínají stejnou hodnotou a mají přednost delší hodnoty. V předchozím ukázkovém souboru *host.json* všechny kategorie, které začínají protokolem Host.Aggregator na `Information` úrovni. Všechny ostatní kategorie, které začínají "Host", například "Host.Executor", protokoluna úrovni. `Error`| 

## <a name="queues"></a>Fronty

Nastavení konfigurace [pro aktivační události a vazby fronty úložiště](functions-bindings-storage-queue.md).

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
|maxPollingInterval|60000|Maximální interval v milisekundách mezi dotazováním fronty.| 
|visibilityTimeout|0|Časový interval mezi opakováním při zpracování zprávy se nezdaří.| 
|batchSize|16|Počet zpráv fronty, které načte čas běhu Functions současně a zpracovává paralelně. Když číslo, které jsou zpracovávány přejde do `newBatchThreshold`, runtime získá další dávku a začne zpracovávat tyto zprávy. Maximální počet souběžných zpráv zpracovávaných na `batchSize` funkci `newBatchThreshold`je tedy plus . Toto omezení platí samostatně pro každou funkci aktivovanou frontou. <br><br>Pokud se chcete vyhnout paralelnímu spuštění zpráv přijatých `batchSize` v jedné frontě, můžete nastavit na 1. Toto nastavení však eliminuje souběžnost pouze tak dlouho, dokud vaše aplikace funkce běží na jednom virtuálním počítači (VM). Pokud se aplikace funkce škáluje na více virtuálních počítačích, každý virtuální počítač může spustit jednu instanci každé funkce aktivované frontou.<br><br>Maximální `batchSize` hodnota je 32. | 
|maxDequeueCount|5|Počet opakování zpracování zprávy před přesunutím do fronty jedovatý.| 
|newBatchThreshold|dávkaVelikost/2|Vždy, když počet zpráv, které jsou zpracovávány současně dostane dolů na toto číslo, runtime načte jinou dávku.| 

## <a name="sendgrid"></a>SendGrid

Nastavení konfigurace pro [výstupní vazbu SendGrind](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|Z|neuvedeno|E-mailová adresa odesílatele ve všech funkcích.| 

## <a name="servicebus"></a>serviceBus

Nastavení konfigurace pro [aktivační události a vazby služby Service Bus](functions-bindings-service-bus.md).

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
|maxConcurrentCalls maxConcurrentCalls maxConcurrentCalls maxCon|16|Maximální počet souběžných volání zpětného volání, které by mělo čerpadlo zprávy zahájit. Ve výchozím nastavení pracuje za běhu functions více zpráv současně. Chcete-li nasměrovat za běhu na zpracování pouze jedné `maxConcurrentCalls` fronty nebo tematické zprávy současně, nastavte na 1. | 
|prefetchCount|neuvedeno|Výchozí PrefetchCount, který bude použit podkladové MessageReceiver.| 
|autoRenewTimeout|00:05:00|Maximální doba, po kterou bude zámek zprávy automaticky obnoven.| 

## <a name="singleton"></a>Singleton

Nastavení konfigurace pro chování zámku Singleton. Další informace naleznete v [tématu GitHub problém o podpoře singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

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
|lockPeriod|00:00:15|Období, které jsou přijata úroveň funkce zámky jsou převzaty. Zámky auto-obnovení.| 
|listenerLockPeriod|00:01:00|Období, které naslouchací proces zámky jsou přijata.| 
|listenerLockRecoveryPollingInterval|00:01:00|Časový interval použitý pro obnovení zámku naslouchací procesu, pokud zámek posluchače nelze získat při spuštění.| 
|lockAcquisitionTimeout|00:01:00|Maximální doba běhu se pokusí získat zámek.| 
|lockAcquisitionPollingInterval|neuvedeno|Interval mezi pokusy o získání zámku.| 

## <a name="tracing"></a>Trasování

*Verze 1.x*

Nastavení konfigurace pro protokoly, které `TraceWriter` vytvoříte pomocí objektu. Viz [Protokolování c#](functions-reference-csharp.md#logging) a [protokolování node.js](functions-reference-node.md#writing-trace-output-to-the-console).

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
|consoleLevel|Info|Úroveň trasování pro protokolování konzoly. Možnosti `off`jsou: `warning` `info`, `error` `verbose`, , a .|
|fileLoggingMode|pouze pro ladění|Úroveň trasování pro protokolování souborů. Možnosti `never` `always`jsou `debugOnly`, , .| 

## <a name="watchdirectories"></a>watchAdresářy

Sada [adresářů sdíleného kódu,](functions-reference-csharp.md#watched-directories) které by měly být sledovány pro změny.  Zajišťuje, že při změně kódu v těchto adresářích jsou změny zachyceny funkcemi.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si, jak aktualizovat soubor host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Zobrazení globálních nastavení v proměnných prostředí](functions-app-settings.md)
