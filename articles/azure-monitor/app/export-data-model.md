---
title: Datový model Přehledy aplikací Azure | Dokumenty společnosti Microsoft
description: Popisuje vlastnosti exportované z průběžného exportu v JSON a používané jako filtry.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: e4dd2310169476e54c06083fee11b2e4cccecd8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663871"
---
# <a name="application-insights-export-data-model"></a>Exportní datový model přehledů aplikací
V této tabulce jsou uvedeny vlastnosti telemetrie odeslané z sad SDK [application insights](../../azure-monitor/app/app-insights-overview.md) na portál.
Tyto vlastnosti se zobrazí ve výstupu dat z [průběžného exportu](export-telemetry.md).
Zobrazují se také ve filtrech vlastností v [Průzkumníku metrik](../../azure-monitor/app/metrics-explorer.md) a [diagnostickém vyhledávání](../../azure-monitor/app/diagnostic-search.md).

Body k poznámce:

* `[0]`v těchto tabulkách označuje bod v cestě, kde je třeba vložit index; ale to není vždy 0.
* Doba trvání je v desetinách mikrosekundy, takže 10000000 == 1 sekunda.
* Data a časy jsou UTC a jsou uvedeny ve formátu ISO`yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Příklad
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Kontext
Všechny typy telemetrie jsou doprovázeny kontextovou částí. Ne všechna tato pole jsou přenášena s každým datovým bodem.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| context.custom.dimensions [0] |objekt [ ] |Dvojice řetězců klíč-hodnota nastavená parametrem vlastních vlastností. Maximální délka klíče 100, hodnoty max délka 1024. Více než 100 jedinečných hodnot, vlastnost může být prohledána, ale nelze ji použít pro segmentaci. Maximálně 200 klíčů za ikey. |
| context.custom.metrics [0] |objekt [ ] |Dvojice klíč-hodnota nastavená parametrem vlastních měření a TrackMetrics. Maximální délka klíče 100, hodnoty mohou být číselné. |
| context.data.eventTime |řetězec |UTC |
| context.data.isSynthetic |Boolean |Zdá se, že požadavek pochází z testu robota nebo webu. |
| context.data.samplingRate |číslo |Procento telemetrie generované sadou SDK, která je odeslána na portál. Rozsah 0,0-100,0. |
| context.device |objekt |Klientské zařízení |
| context.device.browser |řetězec |IE, Chrom, ... |
| context.device.browserVersion |řetězec |Chrome 48.0, ... |
| context.device.deviceModel |řetězec | |
| context.device.deviceName |řetězec | |
| context.device.id |řetězec | |
| context.device.locale |řetězec |en-GB, de-DE, ... |
| context.device.network |řetězec | |
| context.device.oemName |řetězec | |
| context.device.os |řetězec | |
| context.device.osVersion |řetězec |Hostitelský operační systém |
| context.device.roleInstance |řetězec |ID hostitele serveru |
| context.device.roleName |řetězec | |
| context.device.screenResolution |řetězec | |
| context.device.type |řetězec |PC, Prohlížeč, ... |
| context.location |objekt |Odvozeno od clientip. |
| context.location.city |řetězec |Odvozeno od clientip, je-li známo |
| context.location.clientip |řetězec |Poslední osmiúhelník je anonymizován na 0. |
| context.location.continent |řetězec | |
| context.location.country |řetězec | |
| context.location.province |řetězec |Stát nebo provincie |
| context.operation.id |řetězec |Položky, které mají stejné id operace jsou zobrazeny jako související položky na portálu. Obvykle id požadavku. |
| context.operation.name |řetězec |url nebo název požadavku |
| context.operation.parentId |řetězec |Umožňuje vnořené související položky. |
| context.session.id |řetězec |Id skupiny operací ze stejného zdroje. Doba 30 minut bez operace signalizuje konec relace. |
| context.session.isFirst |Boolean | |
| context.user.accountAcquisitionDate |řetězec | |
| context.user.accountId |řetězec | |
| context.user.anonAcquisitionDate |řetězec | |
| context.user.anonId |řetězec | |
| context.user.authAcquisitionDate |řetězec |[Ověřený uživatel](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |řetězec | |
| context.user.isAuthenticated |Boolean | |
| context.user.storeRegion |řetězec | |
| internal.data.documentVersion |řetězec | |
| internal.data.id |řetězec | Jedinečné id, které je přiřazeno při požití položky do přehledů aplikací |

## <a name="events"></a>Události
Vlastní události generované [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| událost [0] počet |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Například 4&gt; = 25 %. |
| název události [0] |řetězec |Název události.  Maximální délka 250. |
| url události [0] |řetězec | |
| událost [0] urlData.base |řetězec | |
| událost [0] urlData.host |řetězec | |

## <a name="exceptions"></a>Výjimky
Hlásí [výjimky](../../azure-monitor/app/asp-net-exceptions.md) na serveru a v prohlížeči.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| sestavení basicException [0] |řetězec | |
| basicException [0] počet |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Například 4&gt; = 25 %. |
| basicException [0] exceptionGroup |řetězec | |
| basicException [0] exceptionType |řetězec | |
| metoda basicException [0] failedUserCodeMethod |řetězec | |
| basicException [0] failedUserCodeAssembly |řetězec | |
| basicException [0] handledAt |řetězec | |
| basicException [0] hasFullStack |Boolean | |
| basicException [0] id |řetězec | |
| metoda basicException [0] |řetězec | |
| zpráva basicException [0] |řetězec |Zpráva o výjimce. Maximální délka 10k. |
| basicException [0] outerExceptionMessage |řetězec | |
| basicException [0] outerExceptionThrownAtAssembly |řetězec | |
| basicException [0] outerExceptionThrownAtMethod |řetězec | |
| basicException [0] outerExceptionType |řetězec | |
| basicException [0] outerId |řetězec | |
| sestavení basicException [0] parsedStack [0] |řetězec | |
| basicException [0] parsedStack [0] fileName |řetězec | |
| úroveň basicException [0] parsedStack [0] |celé číslo | |
| basicException [0] parsedStack [0] řádek |celé číslo | |
| metoda basicException [0] parsedStack [0] |řetězec | |
| zásobníku basicException [0] |řetězec |Maximální délka 10k |
| basicException [0] typeName |řetězec | |

## <a name="trace-messages"></a>Trasovací zprávy
Odesláno [službou TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)a [adaptéry pro protokolování](../../azure-monitor/app/asp-net-trace-logs.md).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| zpráva [0] loggerName |řetězec | |
| parametry zprávy [0] |řetězec | |
| zpráva [0] raw |řetězec |Zpráva protokolu, maximální délka 10k. |
| zpráva [0] závažnostÚroveň |řetězec | |

## <a name="remote-dependency"></a>Vzdálená závislost
Odesláno TrackDependency. Slouží k hlášení výkonu a využití [volání závislostí na](../../azure-monitor/app/asp-net-dependencies.md) serveru a volání AJAX v prohlížeči.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| remoteDependency [0] async |Boolean | |
| remoteDependency [0] baseName |řetězec | |
| remoteDependency [0] commandName |řetězec |Například "home/index" |
| remoteDependency [0] počet |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Například 4&gt; = 25 %. |
| remoteDependency [0] dependencyTypeName |řetězec |HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value |číslo |Čas od volání do dokončení odpovědi závislostí |
| remoteDependency [0] id |řetězec | |
| název remoteDependency [0] |řetězec |Adresu url. Maximální délka 250. |
| remoteDependency [0] resultCode |řetězec |ze závislosti HTTP |
| remoteDependency [0] úspěch |Boolean | |
| typ remoteDependency [0] |řetězec |Http, Sql,... |
| remoteDependency [0] url |řetězec |Maximální délka 2000 |
| remoteDependency [0] urlData.base |řetězec |Maximální délka 2000 |
| remoteDependency [0] urlData.hashTag |řetězec | |
| remoteDependency [0] urlData.host |řetězec |Maximální délka 200 |

## <a name="requests"></a>Žádosti
Odesláno [trackrequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Standardní moduly používají k hlášení doby odezvy serveru měřené na serveru.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| požadavek [0] počet |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Například: 4&gt; = 25 %. |
| request [0] durationMetric.value |číslo |Čas od žádosti do odpovědi. 1e7 == 1s |
| požadavek [0] id |řetězec |Id operace |
| název požadavku [0] |řetězec |GET/POST + url základna.  Maximální délka 250 |
| request [0] responseCode |celé číslo |Odpověď HTTP odeslaná klientovi |
| požadavek [0] úspěch |Boolean |Výchozí == (responseCode &lt; 400) |
| požadavek [0] url |řetězec |Bez hostitele |
| požadavek [0] urlData.base |řetězec | |
| požadavek [0] urlData.hashTag |řetězec | |
| požadavek [0] urlData.host |řetězec | |

## <a name="page-view-performance"></a>Výkon zobrazení stránky
Odesláno prohlížečem. Měří čas zpracování stránky, od uživatele, který zahajuje požadavek na zobrazení dokončena (s výjimkou asynchronních volání AJAX).

Kontextové hodnoty zobrazují klientský operační systém a verzi prohlížeče.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |celé číslo |Čas od konce příjmu HTML až po zobrazení stránky. |
| název clientPerformance [0] |řetězec | |
| clientPerformance [0] networkConnection.value |celé číslo |Doba navázání síťového připojení. |
| clientPerformance [0] receiveRequest.value |celé číslo |Čas od konce odeslání požadavku na příjem HTML v odpovědi. |
| clientPerformance [0] sendRequest.value |celé číslo |Doba od přijetí k odeslání požadavku HTTP. |
| clientPerformance [0] total.value |celé číslo |Čas od zahájení odesílání požadavku na zobrazení stránky. |
| adresa URL výkonu klienta [0] |řetězec |Adresa URL tohoto požadavku |
| clientPerformance [0] urlData.base |řetězec | |
| clientPerformance [0] urlData.hashTag |řetězec | |
| clientPerformance [0] urlData.host |řetězec | |
| clientPerformance [0] urlData.protocol |řetězec | |

## <a name="page-views"></a>Page Views
Odesláno trackPageView() nebo [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| zobrazení [0] počet |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Například 4&gt; = 25 %. |
| zobrazení [0] durationMetric.value |celé číslo |Hodnota volitelně nastavena v trackPageView() nebo startTrackPage() - stopTrackPage(). Není stejný jako clientPerformance hodnoty. |
| zobrazit název [0] |řetězec |Název stránky.  Maximální délka 250 |
| zobrazit adresu URL [0] |řetězec | |
| zobrazení [0] urlData.base |řetězec | |
| zobrazit [0] urlData.hashTag |řetězec | |
| zobrazení [0] urlData.host |řetězec | |

## <a name="availability"></a>Dostupnost
Hlásí [webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| dostupnost [0] availabilityMetric.name |řetězec |dostupnosti |
| dostupnost [0] availabilityMetric.value |číslo |1,0 nebo 0,0 |
| dostupnost [0] počet |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Například 4&gt; = 25 %. |
| dostupnost [0] dataSizeMetric.name |řetězec | |
| availability [0] dataSizeMetric.value |celé číslo | |
| dostupnost [0] durationMetric.name |řetězec | |
| availability [0] durationMetric.value |číslo |Doba trvání testu. 1e7==1s |
| zpráva o dostupnosti [0] |řetězec |Diagnostika selhání |
| výsledek dostupnosti [0] |řetězec |Úspěch/neúspěch |
| dostupnost [0] runLocation |řetězec |Geo zdroj http req |
| dostupnost [0] testName |řetězec | |
| dostupnost [0] testRunId |řetězec | |
| dostupnost [0] testTimestamp |řetězec | |

## <a name="metrics"></a>Metriky
Generováno TrackMetric().

Hodnota metriky se nachází v souboru context.custom.metrics[0]

Například:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>O hodnotách metrik
Hodnoty metrik, v přehledech metrik i jinde, jsou hlášeny se standardní strukturou objektu. Například:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

V současné době - i když to může změnit v budoucnu - `count==1` ve `name` všech `value` hodnot ách hlášených ze standardních modulů SDK a pouze pole a jsou užitečné. Jediný případ, kde by se liší by, pokud napíšete vlastní TrackMetric volání, ve kterém nastavíte další parametry.

Účelem ostatních polí je umožnit agregaci metrik v sadě SDK, aby se snížil provoz na portálu. Před odesláním každé přehledu metrik můžete například zprůměrnit několik po sobě jdoucích čtení. Potom byste vypočítali min, max, směrodatnou odchylku a agregotní hodnotu (součet nebo průměr) a nastavili počet na počet odečtů reprezentovaných sestavou.

Ve výše uvedených tabulkách jsme vynechali počet zřídka používaných polí, min, max, stdDev a sampledValue.

Namísto předběžné agregace metriky, můžete použít [vzorkování,](../../azure-monitor/app/sampling.md) pokud potřebujete snížit objem telemetrie.

### <a name="durations"></a>Doby trvání
Není-li uvedeno jinak, jsou doby trvání reprezentovány v desetinách mikrosekundy, takže 10000000.0 znamená 1 sekundu.

## <a name="see-also"></a>Viz také
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Nepřetržitý export](export-telemetry.md)
* [Ukázky kódu](export-telemetry.md#code-samples)
