---
title: reference host.json pro Funkce Azure 2.x
description: Referenční dokumentace pro soubor host.json Azure Functions s runtime v2.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 3d98be2dcc351aa88b9e126c883865079e407c2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473366"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Reference host.json pro Funkce Azure 2.x a novější 

> [!div class="op_single_selector" title1="Vyberte verzi runtime Azure Functions, kterou používáte: "]
> * [Verze 1](functions-host-json-v1.md)
> * [Verze 2+](functions-host-json.md)

Soubor metadat *host.json* obsahuje možnosti globální konfigurace, které ovlivňují všechny funkce aplikace funkce. Tento článek uvádí nastavení, která jsou k dispozici počínaje verzí 2.x runtime Azure Functions.  

> [!NOTE]
> Tento článek je pro Azure Functions 2.x a novější verze.  Odkaz na host.json ve funkcích 1.x najdete v [tématu reference host.json pro Funkce Azure 1.x](functions-host-json-v1.md).

Další možnosti konfigurace aplikace funkce se spravují v [nastavení aplikace](functions-app-settings.md) (pro nasazené aplikace) nebo v souboru [local.settings.json](functions-run-local.md#local-settings-file) (pro místní vývoj).

Konfigurace v host.json související s vazbami jsou použity stejně pro každou funkci v aplikaci funkce. 

## <a name="sample-hostjson-file"></a>Ukázkový soubor host.json

Následující ukázkový soubor *host.json* pro verzi 2.x+ obsahuje všechny možné možnosti (s výjimkou všech, které jsou určeny pouze pro interní použití).

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

Následující části tohoto článku vysvětlují každou vlastnost nejvyšší úrovně. Všechny jsou volitelné, pokud není uvedeno jinak.

## <a name="aggregator"></a>Agregátor

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Toto nastavení je podřízeným [protokolováním](#logging).

Řídí možnosti pro Application Insights, včetně [možností vzorkování](./functions-monitoring.md#configure-sampling).

Úplnou strukturu JSON naleznete v předchozím [příkladu souboru host.json](#sample-hostjson-file).

> [!NOTE]
> Protokol vzorkování může způsobit, že některé spuštění nezobrazí v okně application insights monitoru. Chcete-li se `samplingExcludedTypes: "Request"` vyhnout `applicationInsights` vzorkování protokolu, přidejte k hodnotě.

| Vlastnost | Výchozí | Popis |
| --------- | --------- | --------- | 
| vzorkováníNastavení | neuvedeno | Viz [applicationInsights.samplingSettings](#applicationinsightssamplingsettings). |
| enableLiveMetrics | true | Povolí shromažďování živých metrik. |
| enableDependencyTracking | true | Umožňuje sledování závislostí. |
| enablePerformanceCountersCollection | true | Povolí kolekci čítačů výkonu Kudu. |
| liveMetricsInitializationDelay | 00:00:15 | Pouze pro interní použití. |
| httpAutoCollectionOptions | neuvedeno | Viz [applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotKonfigurace | neuvedeno | Viz [applicationInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights.samplingSettings

|Vlastnost | Výchozí | Popis |
| --------- | --------- | --------- | 
| Isenabled | true | Povolí nebo zakáže vzorkování. | 
| maxTelemetryItemsPerSecond | 20 | Cílový počet položek telemetrie protokolovaných za sekundu na každém hostiteli serveru. Pokud vaše aplikace běží na mnoha hostitelích, snižte tuto hodnotu tak, aby zůstala v rámci celkové cílové návštěvnosti. | 
| hodnoceníInterval | 01:00:00 | Interval, ve kterém je přehodnocena aktuální rychlost telemetrie. Vyhodnocení se provádí jako klouzavý průměr. Tento interval můžete zkrátit, pokud je telemetrie náchylná k náhlým výbuchům. |
| initialSamplingPercentage| 1.0 | Počáteční procento odběru vzorků použité na začátku procesu odběru vzorků dynamicky mění procento. Nesnižovat hodnotu při ladění. |
| samplingPercentageIncreaseTimeout | 00:00:01 | Když se změní procentuální hodnota vzorkování, tato vlastnost určuje, jak brzy poté application insights je povoleno zvýšit procento vzorkování znovu zachytit více dat. |
| samplingPercentageDecreaseTimeout | 00:00:01 | Když se změní procentuální hodnota vzorkování, tato vlastnost určuje, jak brzy poté application insights je povoleno snížit procento vzorkování znovu zachytit méně dat. |
| minSamplingProcento | 0.1 | Jako procento vzorkování se liší, tato vlastnost určuje minimální povolené procento vzorkování. |
| maxSamplingProcento | 0.1 | Jako procento vzorkování se liší, tato vlastnost určuje maximální povolené procento vzorkování. |
| movingAverageRatio | 1.0 | Při výpočtu klouzavého průměru je hmotnost přiřazena k poslední hodnotě. Použijte hodnotu rovnou nebo menší než 1. Menší hodnoty, aby algoritmus méně reaktivní na náhlé změny. |
| excludedTypy | null | Seznam typů oddělených středníkem, které nechcete vzorkovat. Rozpoznané typy jsou: Závislost, Událost, Výjimka, Zobrazení stránky, Požadavek, Trasování. Všechny instance zadaných typů jsou přenášeny; typy, které nejsou zadány, jsou vzorkovány. |
| includedTypy | null | Seznam typů, které chcete vzorkovat, je sestředním střevem. prázdný seznam zahrnuje všechny typy. Typ uvedený `excludedTypes` v typech přepsání je zde uveden. Rozpoznané typy jsou: Závislost, Událost, Výjimka, Zobrazení stránky, Požadavek, Trasování. Všechny instance zadaných typů jsou přenášeny; typy, které nejsou zadány, jsou vzorkovány. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights.httpAutoCollectionOptions

|Vlastnost | Výchozí | Popis |
| --------- | --------- | --------- | 
| povolithttpTriggerExtendedInfoCollection | true | Povolí nebo zakáže rozšířené informace o požadavku HTTP pro aktivační události PROTOKOLU HTTP: hlavičky korelace příchozích požadavků, podpora klíčů s více instrumentace, metoda HTTP, cesta a odpověď. |
| enableW3CDistributedTracing | true | Povolí nebo zakáže podporu w3c distribuovaného trasovacího protokolu (a zapne starší schéma korelace). Ve výchozím `enableHttpTriggerExtendedInfoCollection` nastavení je povolena, pokud je true. Pokud `enableHttpTriggerExtendedInfoCollection` je false, tento příznak se vztahuje pouze na odchozí požadavky, nikoli příchozí požadavky. |
| enableResponseHeaderInjection | true | Povolí nebo zakáže vkládání vícesložkových korelačních hlaviček do odpovědí. Povolení vkládání umožňuje Application Insights vytvořit mapu aplikace při použití několika instrumentačních klíčů. Ve výchozím `enableHttpTriggerExtendedInfoCollection` nastavení je povolena, pokud je true. Toto nastavení neplatí, pokud `enableHttpTriggerExtendedInfoCollection` je false. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights.snapshotKonfigurace

Další informace o snímcích najdete v [tématu Ladění snímků na výjimky v aplikacích .NET](/azure/azure-monitor/app/snapshot-debugger) a [poradce při potížích s povolením debuggeru snímků přehledů aplikací nebo zobrazení snímků](/azure/azure-monitor/app/snapshot-debugger-troubleshoot).

|Vlastnost | Výchozí | Popis |
| --------- | --------- | --------- | 
| endpoint agenta | null | Koncový bod používaný pro připojení ke službě Debugger snímek Application Insights. Pokud null, je použit výchozí koncový bod. |
| captureSnapshotMemoryWeight | 0,5 | Hmotnost vzhledem k aktuální velikosti paměti procesu při kontrole, zda je dostatek paměti, aby snímek. Očekávaná hodnota je větší než 0 správný zlomek (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | Omezení počtu neúspěšných požadavků na vyžádání snímků před zakázáním telemetrického procesoru.|
| handleUntrackedExceptions | true | Povolí nebo zakáže sledování výjimek, které nejsou sledovány telemetrií Application Insights. |
| Isenabled | true | Povolí nebo zakáže kolekci snímků. | 
| isEnabledInDeveloperMode | false (nepravda) | Povolí nebo zakáže kolekci snímků je povolena v režimu vývojáře. |
| isEnabledWhenProfiling | true | Povolí nebo zakáže vytváření snímků i v případě, že profiler přehledů aplikací shromažďuje podrobnou relaci profilování. |
| isExceptionSnappointsEnabled | false (nepravda) | Povolí nebo zakáže filtrování výjimek. |
| isLowPrioritySnapshotUploader | true | Určuje, zda má být spuštěn proces SnapshotUploader pod normální prioritu. |
| maximální velikost CollectionPlanSize | 50 | Maximální počet problémů, které můžeme sledovat kdykoliv v rozsahu od jednoho do 9999. |
| maximumSnapshotsRequired | 3 | Maximální počet snímků shromážděných pro jeden problém v rozsahu od jednoho do 999. Problém může být myšlenka jako jednotlivé throw prohlášení ve vaší aplikaci. Jakmile počet snímků shromážděných pro problém dosáhne této hodnoty, žádné další snímky budou shromažďovány pro `problemCounterResetInterval`tento problém, dokud jsou resetovány čítače problému (viz) a je znovu dosaženo limitu. `thresholdForSnapshotting` |
| problémCounterResetInterval | 24:00:00 | Jak často se resetují čítače problémů v rozsahu od jedné minuty do sedmi dnů. Po dosažení tohoto intervalu jsou všechny počty problémů nastaveny na nulu. Existující problémy, které již dosáhly prahové hodnoty pro vytváření snímků, ale `maximumSnapshotsRequired`ještě negenerovaly počet snímků v aplikaci , zůstávají aktivní. |
| poskytnoutAnonymousTelemetrie | true | Určuje, zda má být společnosti Microsoft odeslána anonymní telemetrie o použití a chybě. Tato telemetrická data může být použita, pokud se obrátíte na společnost Microsoft, abyste pomohli vyřešit problémy s ladicím programem snímků. Používá se také ke sledování vzorců využití. |
| interval opětovného připojení | 00:15:00 | Jak často se znovu připojíme ke koncovému bodu ladicího programu snímek. Povolený dosah je jedna minuta až jeden den. |
| shadowCopyFolder | null | Určuje složku, která má být používána pro stínovou kopírování binárních souborů. Pokud není nastavena, složky určené následující proměnné prostředí jsou vyzkoušeny v pořadí: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Pokud true, pouze jedna instance SnapshotUploader bude shromažďovat a nahrávat snímky pro více aplikací, které sdílejí InstrumentationKey. Pokud je nastavena na false, SnapshotUploader bude jedinečný pro každou řazenou skupinu (ProcessName, InstrumentationKey). |
| snapshotInLowPriorityThread | true | Určuje, zda chcete zpracovat snímky ve vlákně s nízkou prioritou vi. Vytvoření snímku je rychlá operace, ale chcete-li nahrát snímek do služby ladicí program snímku, musí být nejprve zapsánna disk jako minidump. To se stane v procesu SnapshotUploader. Nastavení této hodnoty na true používá vi s nízkou prioritou k zápisu minidump, který nebude soutěžit s vaší aplikace pro prostředky. Nastavení této hodnoty na false urychluje vytváření minidump na úkor zpomalení aplikace. |
| snapshotsPerDayLimit | 30 | Maximální počet snímků povolených za jeden den (24 hodin). Toto omezení je také vynuceno na straně služby Application Insights. Nahrávání je omezeno na 50 za den na aplikaci (tj. na klíč instrumentace). Tato hodnota pomáhá zabránit vytváření dalších snímků, které budou nakonec odmítnuty během nahrávání. Hodnota nula zcela odebere limit, což se nedoporučuje. |
| snapshotsPerTenMinutesLimit | 1 | Maximální počet snímků povolených za 10 minut. I když neexistuje žádná horní mez na tuto hodnotu, buďte opatrní zvýšení min. na produkční úlohy, protože by to mohlo mít vliv na výkon vaší aplikace. Vytvoření snímku je rychlé, ale vytvoření minidump snímku a jeho nahrání do služby Debugger snímek je mnohem pomalejší operace, která bude soutěžit s vaší aplikace pro prostředky (procesor a vstupně-v). |
| tempFolder | null | Určuje složku pro zápis minidumpů a souborů protokolu uploaderu. Pokud není nastaveno, použije se *%TEMP%\Dumps.* |
| thresholdForSnapshotting | 1 | Kolikrát Application Insights potřebuje zobrazit výjimku, než požádá o snímky. |
| uploaderProxy | null | Přepíše proxy server použitý v procesu nahrávání snímků. Toto nastavení může být nutné použít, pokud se aplikace připojuje k internetu prostřednictvím proxy serveru. Kolektor snímků běží v rámci procesu aplikace a bude používat stejné nastavení proxy serveru. Správce nahrávání snímků je však spuštěn jako samostatný proces a možná budete muset nakonfigurovat proxy server ručně. Pokud je tato hodnota null, pokusí se nástroj Snapshot Collector automaticky rozpoznat adresu proxy kontrolou system.net.webrequest.defaultwebproxy a předáním hodnoty uživateli snímek. Pokud tato hodnota není null, pak se nepoužívá automatické zjišťování a proxy server zadaný zde bude použit v snímek Uploader. |

## <a name="cosmosdb"></a>cosmosDb

Nastavení konfigurace lze nalézt v [spouštěčů a vazbách Cosmos DB](functions-bindings-cosmosdb-v2-output.md#host-json).

## <a name="durabletask"></a>odolnýÚkol

Nastavení konfigurace lze nalézt v [vazbách pro trvalé funkce](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Nastavení konfigurace lze nalézt v [aktivační události event hubu a vazby](functions-bindings-event-hubs-output.md#host-json). 

## <a name="extensions"></a>Rozšíření

Vlastnost, která vrací objekt, který obsahuje všechna nastavení specifická pro vazbu, například [http](#http) a [eventHub](#eventhub).

## <a name="extensionbundle"></a>rozšířeníBundle 

Rozšíření balíčky umožňují přidat kompatibilní sadu funkce rozšíření vazby do aplikace funkce. Další informace naleznete v [tématu Rozšíření balíčků pro místní rozvoj](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a> – funkce

Seznam funkcí, které hostitel úlohy spustí. Prázdné pole znamená spustit všechny funkce. Určeno pro použití pouze při [místním spuštění](functions-run-local.md). Ve funkčních aplikacích v Azure byste místo toho měli postupovat podle pokynů v [části Jak zakázat funkce ve službě Azure Functions](disable-function.md) a zakázat konkrétní funkce, místo abyste používali toto nastavení.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Označuje dobu trvání časového období pro všechny funkce. Následuje formát řetězce timespan. V plánu spotřeba bez serveru je platný rozsah od 1 sekundy do 10 minut a výchozí hodnota je 5 minut.  

V plánu Premium je platný rozsah od 1 sekundy do 60 minut a výchozí hodnota je 30 minut.

V plánu Vyhrazené (App Service) neexistuje žádné celkové omezení a výchozí hodnota je 30 minut. Hodnota `-1` označuje neohraničené spuštění, ale doporučuje se zachovat pevnou horní mez.

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

Nastavení konfigurace naleznete v [http spouštěčů a vazeb](functions-bindings-http-webhook-output.md#hostjson-settings).

## <a name="logging"></a>protokolování

Řídí chování protokolování aplikace funkce, včetně Application Insights.

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
|fileLoggingMode|pouze pro ladění|Definuje, jaká úroveň protokolování souborů je povolena.  Možnosti `never` `always`jsou `debugOnly`, , . |
|Loglevel|neuvedeno|Objekt, který definuje filtrování kategorie protokolu pro funkce v aplikaci. Verze 2.x a novější postupujte podle ASP.NET rozložení Jádra pro filtrování kategorií protokolů. Toto nastavení umožňuje filtrovat protokolování pro konkrétní funkce. Další informace naleznete v [tématu Filtrování protokolů](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) v dokumentaci ASP.NET Jádra. |
|konzola|neuvedeno| Nastavení protokolování [konzoly.](#console) |
|applicationInsights|neuvedeno| [ApplicationInsights](#applicationinsights) nastavení. |

## <a name="console"></a>konzola

Toto nastavení je podřízeným [protokolováním](#logging). Řídí protokolování konzoly, když není v režimu ladění.

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
|Isenabled|false (nepravda)|Povolí nebo zakáže protokolování konzoly.| 

## <a name="manageddependency"></a>managedDependency

Spravovaná závislost je funkce, která je aktuálně podporována pouze s funkcemi založenými na prostředí PowerShell. Umožňuje závislosti, které mají být automaticky spravovány službou. Pokud `enabled` je vlastnost `true`nastavena `requirements.psd1` na , soubor je zpracován. Závislosti jsou aktualizovány při vydání všech dílčích verzí. Další informace najdete v článku [Spravované závislosti](functions-reference-powershell.md#dependency-management) v powershellu.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>Fronty

Nastavení konfigurace najdete v [aktivačních událostech a vazbách fronty úložiště](functions-bindings-storage-queue-output.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Nastavení konfigurace lze nalézt v [Triggers sendgrid a vazby](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Nastavení konfigurace naleznete v [aktivačních událostech a vazbách service bus .](functions-bindings-service-bus-output.md#host-json)

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

## <a name="version"></a>version

Tato hodnota označuje verzi schématu host.json. Řetězec `"version": "2.0"` verze je vyžadován pro aplikaci funkce, která se zaměřuje na v2 runtime nebo novější verzi. Neexistují žádné změny schématu host.json mezi v2 a v3.

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
