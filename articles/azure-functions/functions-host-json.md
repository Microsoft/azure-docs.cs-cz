---
title: host.jsna referenci pro Azure Functions 2. x
description: Referenční dokumentace pro Azure Functions host.jsv souboru s modulem runtime v2.
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 735c92720f4a3f871499ad3a0565446a02b438eb
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654808"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Referenční informace k souboru host.json pro Azure Functions 2.x a novější 

> [!div class="op_single_selector" title1="Vyberte verzi Azure Functions runtime, kterou používáte: "]
> * [Verze 1](functions-host-json-v1.md)
> * [Verze 2 +](functions-host-json.md)

*host.jsv* souboru metadat obsahuje možnosti globální konfigurace, které mají vliv na všechny funkce aplikace Function App. Tento článek obsahuje seznam nastavení, která jsou k dispozici od verze 2. x Azure Functions runtime.  

> [!NOTE]
> Tento článek je určen pro Azure Functions 2. x a novější verze.  Odkaz na host.jspro ve funkcích 1. x naleznete v tématu [host.json reference for Azure Functions 1. x](functions-host-json-v1.md).

Další možnosti konfigurace aplikace Function App jsou spravované v [nastavení aplikace](functions-app-settings.md) (pro nasazené aplikace) nebo v [local.settings.jsv](functions-run-local.md#local-settings-file) souboru (pro místní vývoj).

Konfigurace v host.jsv souvislosti s vazbami se aplikují rovnoměrně na každou funkci aplikace Function App. 

Můžete také [přepsat nebo použít nastavení pro každé prostředí](#override-hostjson-values) pomocí nastavení aplikace.

## <a name="sample-hostjson-file"></a>Ukázka host.jssouboru

Následující vzorový *host.js* souboru pro verzi 2. x + obsahuje všechny možné možnosti (kromě těch, které jsou určené pouze pro interní použití).

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
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
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
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "retry": {
      "strategy": "fixedDelay",
      "maxRetryCount": 5,
      "delayInterval": "00:00:05"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "watchFiles": [ "myFile.txt" ]
}
```

Následující části tohoto článku vysvětlují jednotlivé vlastnosti nejvyšší úrovně. Všechny jsou volitelné, pokud není uvedeno jinak.

## <a name="aggregator"></a>agregovan

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Toto nastavení je podřízenou položkou [protokolování](#logging).

Řídí možnosti pro Application Insights, včetně [možností vzorkování](./configure-monitoring.md#configure-sampling).

Úplnou strukturu JSON naleznete v předchozím [příkladu host.jssouboru](#sample-hostjson-file).

> [!NOTE]
> Vzorkování protokolu může způsobit, že se některá spuštění v okně monitorování Application Insights neprojeví. Chcete-li se vyhnout vzorkování protokolu, přidejte `excludedTypes: "Request"` k `samplingSettings` hodnotě.

| Vlastnost | Výchozí | Popis |
| --------- | --------- | --------- | 
| samplingSettings | neuvedeno | Viz [applicationInsights. samplingSettings](#applicationinsightssamplingsettings). |
| enableLiveMetrics | true | Povoluje shromažďování živých metrik. |
| enableDependencyTracking | true | Povolí sledování závislostí. |
| enablePerformanceCountersCollection | true | Povolí shromažďování čítačů výkonu Kudu. |
| liveMetricsInitializationDelay | 00:00:15 | Pouze pro interní použití. |
| httpAutoCollectionOptions | neuvedeno | Viz [applicationInsights. httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguration | neuvedeno | Viz [applicationInsights. snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights. samplingSettings

Další informace o těchto nastaveních najdete [v tématu vzorkování v Application Insights](../azure-monitor/app/sampling.md). 

|Vlastnost | Výchozí | Popis |
| --------- | --------- | --------- | 
| isEnabled | true | Povolí nebo zakáže vzorkování. | 
| maxTelemetryItemsPerSecond | 20 | Cílový počet položek telemetrie protokolovaných za sekundu na každém hostiteli serveru. Pokud vaše aplikace běží na mnoha hostitelích, snižte tuto hodnotu tak, aby zůstala v rámci celkové cílové míry provozu. | 
| evaluationInterval | 01:00:00 | Interval, ve kterém je aktuální frekvence telemetrie znovu vyhodnocena. Hodnocení se provádí jako klouzavý průměr. Pokud je vaše telemetrie příčinou náhlého nárůstu, možná budete chtít tento interval zkrátit. |
| initialSamplingPercentage| 100,0 | Počáteční procentuální hodnota vzorkování použitá na začátku procesu vzorkování, která dynamicky mění procento. Při ladění nezmenšujte hodnotu. |
| samplingPercentageIncreaseTimeout | 00:00:01 | Když se změní procentuální hodnota vzorkování, tato vlastnost určuje, jak brzo Application Insights může znovu vyvolat procento vzorkování a zachytit další data. |
| samplingPercentageDecreaseTimeout | 00:00:01 | Když se změní procentuální hodnota vzorkování, tato vlastnost určuje, jak brzo Application Insights může snížit procento vzorkování znovu a zachytit tak méně dat. |
| minSamplingPercentage | 0,1 | Když se procento vzorkování liší, tato vlastnost určuje minimální povolený procentuální podíl vzorkování. |
| maxSamplingPercentage | 100,0 | Když se procento vzorkování liší, tato vlastnost určuje maximální povolené procento vzorkování. |
| movingAverageRatio | 1.0 | Při výpočtu klouzavého průměru je váha přiřazená k nejnovější hodnotě. Použijte hodnotu rovnou nebo menší než 1. Menší hodnoty nastaví algoritmus méně aktivní na náhlé změny. |
| excludedTypes | null | Středníkem oddělený seznam typů, které nechcete vzorkovat. Rozpoznané typy jsou: `Dependency` , `Event` , `Exception` , `PageView` , a `Request` `Trace` . Jsou přenášeny všechny instance zadaných typů; typy, které nejsou určeny, jsou vzorkované. |
| includedTypes | null | Seznam typů, které chcete vzorkovat, oddělený středníky; prázdný seznam zahrnuje všechny typy. Typ uvedený v seznamu přepsat typy, které jsou `excludedTypes` zde uvedeny. Rozpoznané typy jsou: `Dependency` , `Event` , `Exception` , `PageView` , a `Request` `Trace` . Instance zadaných typů jsou vzorkované; typy, které nejsou zadány nebo implicitně jsou přenášeny bez vzorkování. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights. httpAutoCollectionOptions

|Vlastnost | Výchozí | Popis |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | Povoluje nebo zakazuje rozšířené informace o požadavku HTTP pro aktivační události protokolu HTTP: příchozí hlavičky žádostí o relaci, podpora klíčů pro více instrumentací, metoda HTTP, cesta a odpověď. |
| enableW3CDistributedTracing | true | Povolí nebo zakáže podporu protokolu W3C Distributed Tracing Protocol (a zapne starší verzi schématu korelace). Pokud `enableHttpTriggerExtendedInfoCollection` má hodnotu true, je ve výchozím nastavení povolená. Pokud `enableHttpTriggerExtendedInfoCollection` je hodnota false, vztahuje se tento příznak pouze na odchozí požadavky, nikoli na příchozí požadavky. |
| enableResponseHeaderInjection | true | Povolí nebo zakáže vkládání hlaviček korelace s více komponentami do odpovědí. Povolení injektáže umožňuje Application Insights sestavit mapu aplikace, když se používají několik klíčů instrumentace. Pokud `enableHttpTriggerExtendedInfoCollection` má hodnotu true, je ve výchozím nastavení povolená. Toto nastavení se nepoužije `enableHttpTriggerExtendedInfoCollection` , pokud má hodnotu false. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights. snapshotConfiguration

Další informace o snímcích najdete v tématu [ladění snímků při výjimkách v aplikacích .NET](../azure-monitor/app/snapshot-debugger.md) a řešení potíží s [povolením Application Insights Snapshot Debugger nebo zobrazením snímků](../azure-monitor/app/snapshot-debugger-troubleshoot.md).

|Vlastnost | Výchozí | Popis |
| --------- | --------- | --------- | 
| agentEndpoint | null | Koncový bod, který se používá pro připojení ke službě Application Insights Snapshot Debugger. Pokud je null, použije se výchozí koncový bod. |
| captureSnapshotMemoryWeight | 0,5 | Váha přidělená aktuální velikosti paměti procesu při kontrole, zda je k dispozici dostatek paměti pro pořízení snímku. Očekávaná hodnota je větší než 0 správný zlomek (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | Omezení počtu neúspěšných žádostí o snímky, než je procesor telemetrie zakázán.|
| handleUntrackedExceptions | true | Povolí nebo zakáže sledování výjimek, které nejsou sledovány Application Insights telemetrie. |
| isEnabled | true | Povolí nebo zakáže shromažďování snímků. | 
| isEnabledInDeveloperMode | false (nepravda) | Povolí nebo zakáže shromažďování snímků v režimu pro vývojáře. |
| isEnabledWhenProfiling | true | Povolí nebo zakáže vytváření snímků, i když Application Insights Profiler shromažďuje podrobnou relaci profilování. |
| isExceptionSnappointsEnabled | false (nepravda) | Povolí nebo zakáže filtrování výjimek. |
| isLowPrioritySnapshotUploader | true | Určuje, zda spustit proces SnapshotUploader pod normální prioritou. |
| maximumCollectionPlanSize | 50 | Maximální počet problémů, které můžeme kdykoli sledovat v rozsahu od 1 do 9999. |
| maximumSnapshotsRequired | 3 | Maximální počet snímků shromážděných pro jeden problém v rozsahu od 1 do 999. Problém může být v aplikaci považován za individuální příkaz throw. Jakmile počet snímků shromážděných pro daný problém dosáhne této hodnoty, nebudou pro tento problém shromažďovány žádné další snímky, dokud nebudou čítače problémů obnoveny (viz `problemCounterResetInterval` ) a `thresholdForSnapshotting` je dosaženo limitu. |
| problemCounterResetInterval | 24:00:00 | Jak často se mají resetovat čítače problémů v rozsahu od 1 minuty do sedmi dnů. Po dosažení tohoto intervalu se všechny počty problémů resetují na nula. Stávající problémy, které již dosáhly prahové hodnoty pro vytváření snímků, ale dosud negenerovaly počet snímků v `maximumSnapshotsRequired` , zůstávají aktivní. |
| provideAnonymousTelemetry | true | Určuje, jestli se má Microsoftu odesílat anonymní využití a chybové telemetrie. Tato telemetrie se dá použít, pokud se obrátíte na Microsoft, abyste pomohli řešit problémy s Snapshot Debugger. Používá se také k monitorování vzorců používání. |
| reconnectInterval | 00:15:00 | Jak často se znovu připojujeme ke koncovému bodu Snapshot Debugger. Povolený rozsah je 1 minuta až jeden den. |
| shadowCopyFolder | null | Určuje složku, která se má použít pro stínové kopírování binárních souborů. Pokud není nastavené, vyzkouší se složky určené následujícími proměnnými prostředí v pořadí: Fabric_Folder_App_Temp, LOCALAPPDATA, data a TEMP. |
| shareUploaderProcess | true | V případě hodnoty true bude shromažďovat a nahrávat snímky pro několik aplikací, které sdílí InstrumentationKey, jenom jedna instance SnapshotUploader. Pokud je nastavena hodnota false, bude SnapshotUploader jedinečný pro každou řazenou kolekci členů (Process, InstrumentationKey). |
| snapshotInLowPriorityThread | true | Určuje, zda se mají zpracovávat snímky ve vlákně s nízkým/v prioritou. Vytvoření snímku je rychlá operace, ale aby bylo možné nahrát snímek do služby Snapshot Debugger, musí být nejprve zapsán na disk jako s minimálním výpisem. K tomu dochází v procesu SnapshotUploader. Pokud nastavíte hodnotu true, použije se pro zápis s minimálním výpisem v/v s nízkou prioritou, takže se vaše aplikace nebude konkurovat za prostředky. Nastavením této hodnoty na false zrychlí s minimálním výpisem vytváření na úkor zpomalení aplikace. |
| snapshotsPerDayLimit | 30 | Maximální počet snímků povolených za jeden den (24 hodin). Toto omezení se taky vynutilo na straně Application Insights služby. Nahrávání jsou pro jednotlivé aplikace omezené na 50 a den (tj. na klíč instrumentace). Tato hodnota pomáhá zabránit vytváření dalších snímků, které se nakonec během nahrávání odmítnou. Hodnota nula odstraní celý limit, což se nedoporučuje. |
| snapshotsPerTenMinutesLimit | 1 | Maximální počet snímků povolených za 10 minut. I když na této hodnotě není žádná horní mez, požádejte o zvýšení opatrnosti v produkčních úlohách, protože by to mohlo mít vliv na výkon aplikace. Vytvoření snímku je rychlé, ale vytvoření s minimálním výpisem snímku a jeho nahrání do služby Snapshot Debugger je mnohem pomalejší operace, která bude konkurovat vaší aplikaci pro prostředky (procesor i I/O). |
| tempFolder | null | Určuje složku pro zápis souborů protokolu Mini výpisy a odeslání. Pokud není nastavená, použije se *%TEMP%\Dumps* . |
| thresholdForSnapshotting | 1 | Kolikrát Application Insights nutné zobrazit výjimku před tím, než bude požádána o snímky. |
| uploaderProxy | null | Přepíše proxy server použitou v procesu odeslání snímku. Toto nastavení může být nutné použít, pokud se vaše aplikace připojí k Internetu prostřednictvím proxy server. Snapshot Collector běží v rámci procesu aplikace a bude používat stejné nastavení proxy serveru. Odeslání snímku se ale spustí jako samostatný proces a možná budete muset proxy server nakonfigurovat ručně. Pokud je tato hodnota null, Snapshot Collector se pokusí automaticky zjistit adresu proxy pomocí prověření System .NET. WebRequest. DefaultWebProxy a předáním hodnoty pro odeslání snímku. Pokud tato hodnota není null, nepoužije se možnost autodetection a v nástroji pro odeslání snímku se použije proxy server, kterou tady zadáte. |

## <a name="cosmosdb"></a>cosmosDb

Nastavení konfigurace najdete v [Cosmos DB triggerech a vazbách](functions-bindings-cosmosdb-v2-output.md#host-json).

## <a name="customhandler"></a>customHandler

Konfigurační nastavení vlastní obslužné rutiny. Další informace naleznete v tématu [Azure Functions vlastní obslužné rutiny](functions-custom-handlers.md#configuration).

```json
"customHandler": {
  "description": {
    "defaultExecutablePath": "server",
    "workingDirectory": "handler",
    "arguments": [ "--port", "%FUNCTIONS_CUSTOMHANDLER_PORT%" ]
  },
  "enableForwardingHttpRequest": false
}
```

|Vlastnost | Výchozí | Popis |
| --------- | --------- | --------- |
| defaultExecutablePath | neuvedeno | Spustitelný soubor, který se spustí jako vlastní proces obslužné rutiny. Je vyžadováno nastavení při použití vlastních obslužných rutin a její hodnota je relativní vzhledem k kořenu aplikace Function App. |
| workingDirectory | *kořen aplikace Function App* | Pracovní adresář, ve kterém má být spuštěn vlastní proces obslužné rutiny. Je to volitelné nastavení a jeho hodnota je relativní vzhledem k kořenu aplikace Function App. |
| náhodné | neuvedeno | Pole argumentů příkazového řádku, které se má předat procesu vlastní obslužné rutiny. |
| enableForwardingHttpRequest | false (nepravda) | Pokud se nastaví všechny funkce, které se skládají jenom z triggeru HTTP a výstupu HTTP, přepošle původní požadavek HTTP namísto [datové části žádosti](functions-custom-handlers.md#request-payload)vlastní obslužné rutiny. |

## <a name="durabletask"></a>durableTask

Nastavení konfigurace lze nalézt v [vazby pro Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Nastavení konfigurace najdete v [aktivačních událostech a vazbách centra událostí](functions-bindings-event-hubs-trigger.md#host-json). 

## <a name="extensions"></a>SND

Vlastnost, která vrací objekt, který obsahuje všechna nastavení specifická pro vazbu, například [http](#http) a [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

Sady rozšíření umožňují přidat do aplikace Function App kompatibilní sadu funkcí pro vazby. Další informace najdete v tématu [rozšiřující sady pro místní vývoj](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a> – funkce

Seznam funkcí, které hostitel úlohy spouští. Prázdné pole znamená spuštění všech funkcí. Určeno pro použití pouze při [místním spuštění](functions-run-local.md). V aplikacích Function App v Azure byste měli místo použití tohoto nastavení zakázat konkrétní funkce pomocí postupu v [Azure Functions](disable-function.md) .

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Označuje dobu trvání časového limitu pro všechny funkce. Postupuje podle formátu řetězce TimeSpan. 

| Typ plánu | Výchozí (min.) | Maximum (min) |
| -- | -- | -- |
| Consumption | 5 | 10 |
| Premium<sup>1</sup> | 30 | -1 (nevázané)<sup>2</sup> |
| Vyhrazeno (App Service) | 30 | -1 (nevázané)<sup>2</sup> |

<sup>1</sup> provádění plánu Premium je zaručené jenom za 60 minut, ale technicky bez omezení.   
<sup>2</sup> hodnota `-1` označuje neohraničené spouštění, ale doporučuje se zachovat pevnou horní mez.

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

Nastavení konfigurace najdete v [aktivačních událostech http a vazbách](functions-bindings-http-webhook-output.md#hostjson-settings).

## <a name="logging"></a>protokolování

Řídí chování protokolování aplikace Function App, včetně Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
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
|fileLoggingMode|debugOnly|Definuje, jakou úroveň protokolování souborů je povoleno.  Možnosti jsou `never` , `always` , `debugOnly` . |
|logLevel|neuvedeno|Objekt, který definuje filtrování kategorií protokolů pro funkce v aplikaci. Verze 2. x a novější postupují podle ASP.NET Core rozložení pro filtrování kategorií protokolů. Toto nastavení umožňuje filtrovat protokolování pro konkrétní funkce. Další informace najdete v tématu [filtrování protokolů](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering) v dokumentaci k ASP.NET Core. |
|konzola|neuvedeno| Nastavení protokolování [konzoly](#console) . |
|applicationInsights|neuvedeno| Nastavení [applicationInsights](#applicationinsights) |

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

Spravovaná závislost je funkce, kterou momentálně podporuje jenom funkce založené na PowerShellu. Umožňuje, aby se závislosti automaticky spravovaly službou. Když `enabled` je vlastnost nastavena na `true` , `requirements.psd1` je zpracován soubor. Závislosti se aktualizují při vydání jakýchkoli dílčích verzí. Další informace najdete v tématu [spravovaná závislost](functions-reference-powershell.md#dependency-management) v článku věnovaném prostředí PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>vytvořil

Nastavení konfigurace najdete v [aktivačních událostech a vazbách fronty úložiště](functions-bindings-storage-queue-output.md#host-json).  

## <a name="retry"></a>retry

Řídí možnosti [zásad opakování](./functions-bindings-error-pages.md#retry-policies-preview) pro všechna spuštění v aplikaci.

```json
{
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 2,
        "delayInterval": "00:00:03"  
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|strategie|null|Povinná hodnota. Používaná strategie opakování. Platné hodnoty jsou `fixedDelay` nebo `exponentialBackoff` .|
|maxRetryCount|null|Povinná hodnota. Maximální počet opakovaných pokusů povolených pro spuštění funkce. `-1` způsob, jak to provést po neomezenou dobu.|
|delayInterval|null|Zpoždění používané mezi opakovanými pokusy pomocí `fixedDelay` strategie.|
|minimumInterval|null|Minimální prodleva při opakovaném pokusu při použití `exponentialBackoff` strategie.|
|maximumInterval|null|Maximální prodleva při opakovaném pokusu při použití `exponentialBackoff` strategie.| 

## <a name="sendgrid"></a>sendGrid

Nastavení konfigurace najdete v [SendGrid triggerech a vazbách](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Nastavení konfigurace najdete v [Service Bus triggerech a vazbách](functions-bindings-service-bus-output.md#host-json).

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

## <a name="version"></a>verze

Tato hodnota označuje verzi schématu host.js. `"version": "2.0"`Pro aplikaci funkcí, která cílí na modul runtime v2 nebo na novější verzi, se vyžaduje řetězec verze. Neexistují žádné host.jske změnám schématu mezi v2 a v3.

## <a name="watchdirectories"></a>watchDirectories

Sada [sdílených adresářů kódu](functions-reference-csharp.md#watched-directories) , které by měly být monitorovány pro změny.  Zajistí, že při změně kódu v těchto adresářích jsou změny převzaty funkcemi.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="watchfiles"></a>watchFiles

Pole jednoho nebo více názvů souborů, které jsou monitorovány pro změny, které vyžadují restartování vaší aplikace.  To zaručuje, že při změně kódu v těchto souborech jsou aktualizace převzaty funkcemi.

```json
{
    "watchFiles": [ "myFile.txt" ]
}
```

## <a name="override-hostjson-values"></a>Přepsat host.jsna hodnoty

Můžou nastat případy, kdy chcete nakonfigurovat nebo změnit konkrétní nastavení v host.jssouboru pro konkrétní prostředí, aniž byste museli měnit host.jsna samotném souboru.  Můžete přepsat konkrétní host.jspro hodnoty vytváření ekvivalentní hodnoty jako nastavení aplikace. Pokud modul runtime nalezne nastavení aplikace ve formátu `AzureFunctionsJobHost__path__to__setting` , přepíše ekvivalentní host.jsk nastavení umístěnému v `path.to.setting` kódu JSON. Pokud je vyjádřena jako nastavení aplikace, tečka ( `.` ) použitá k označení hierarchie JSON je nahrazena dvojitým podtržítkem ( `__` ). 

Řekněme například, že jste chtěli zakázat vzorkování Application Insight při místním spuštění. Pokud jste změnili místní host.jsv souboru, aby se zakázala Application Insights, může se tato změna během nasazování zobrazit v produkční aplikaci. Bezpečnějším způsobem je vytvořit `"AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"` v souboru nastavení aplikace `local.settings.json` . Můžete to zobrazit v následujícím `local.settings.json` souboru, který není publikovaný:

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "{storage-account-connection-string}",
        "FUNCTIONS_WORKER_RUNTIME": "{language-runtime}",
        "AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"
    }
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o tom, jak aktualizovat host.jsv souboru](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Zobrazit globální nastavení v proměnných prostředí](functions-app-settings.md)
