---
title: Azure Application Insights datový Model | Microsoft Docs
description: Popisuje vlastnosti exportován průběžné exportu ve formátu JSON a použít jako filtry.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: mbullwin
ms.openlocfilehash: ee6597b78ac8de8fc3a7f3796010f22919243b23
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294890"
---
# <a name="application-insights-export-data-model"></a>Application Insights Export datového modelu
Tato tabulka uvádí vlastnosti telemetrická data odesílaná z [Application Insights](app-insights-overview.md) sady SDK k portálu.
Zobrazí se tyto vlastnosti v datovým výstupem z [průběžné exportovat](app-insights-export-telemetry.md).
Zobrazí se také v filtry vlastností v [Explorer metrika](app-insights-metrics-explorer.md) a [diagnostické vyhledávání](app-insights-diagnostic-search.md).

Všimněte si body:

* `[0]` v těchto tabulkách označuje bod v cestě, kde je nutné vložit index; ale není vždy 0.
* Dobách trvání jsou v desetin mikrosekund, takže 10000000 == 1 sekunda.
* Data a časy jsou UTC a jsou uvedeny ve formátu ISO `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Příklad:
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
Všechny typy telemetrických dat se předěl doprovází oddíl kontextu. Ne všechny z těchto polí, se přenáší se každý datový bod.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| Context.Custom.Dimensions [0] |objekt] |Páry klíč hodnota řetězce nastavit parametrem vlastní vlastnosti. Maximální délka klíče 100, hodnoty maximální délky 1024. Více než 100 jedinečné hodnoty vlastnosti lze vyhledat, ale nelze použít v případě segmentace. 200 maximální počet klíčů na ikey. |
| Context.Custom.Metrics [0] |objekt] |Nastavte parametr vlastní měření a TrackMetrics páry klíč hodnota. Maximální délka klíče 100, mohou být číselné hodnoty. |
| context.data.eventTime |řetězec |Univerzální koordinovaný čas (UTC) |
| context.data.isSynthetic |Boolean |Žádost se zdá být od robota nebo webový test. |
| context.data.samplingRate |číslo |Procento telemetrii vygenerovanou sadou SDK, která je odeslána na portál. V rozsahu 0,0 100.0. |
| Context.Device |objekt |Klientské zařízení |
| Context.Device.Browser |řetězec |IE Chrome... |
| context.device.browserVersion |řetězec |Chrome 48,0... |
| context.device.deviceModel |řetězec | |
| context.device.deviceName |řetězec | |
| Context.Device.ID |řetězec | |
| Context.Device.Locale |řetězec |de-DE, en-GB... |
| Context.Device.Network |řetězec | |
| context.device.oemName |řetězec | |
| context.device.osVersion |řetězec |Hostitelský operační systém |
| context.device.roleInstance |řetězec |ID hostitelského serveru |
| context.device.roleName |řetězec | |
| Context.Device.Type |řetězec |Počítač, prohlížeč... |
| Context.Location |objekt |Odvozená od když. |
| Context.location.City |řetězec |Odvozené když, pokud je znám |
| Context.location.ClientIP |řetězec |Poslední Osmiúhelník je anonymní na hodnotu 0. |
| Context.location.Continent |řetězec | |
| Context.location.Country |řetězec | |
| Context.location.Province |řetězec |Kraj |
| Context.Operation.ID |řetězec |Položky, které mají stejné id operace se zobrazují jako související položky v portálu. Obvykle id požadavku. |
| Context.Operation.Name |řetězec |Adresa URL nebo žádosti o název |
| context.operation.parentId |řetězec |Umožňuje vnořené související položky. |
| Context.Session.ID |řetězec |ID skupiny operací z jednoho zdroje. Po dobu 30 minut bez operace signalizuje ukončení relace. |
| context.session.isFirst |Boolean | |
| context.user.accountAcquisitionDate |řetězec | |
| context.user.anonAcquisitionDate |řetězec | |
| context.user.anonId |řetězec | |
| context.user.authAcquisitionDate |řetězec |[Ověřený uživatel](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated |Boolean | |
| internal.data.documentVersion |řetězec | |
| internal.data.ID |řetězec | Jedinečné id, který se přiřazuje při je konzumována položku Application insights |

## <a name="events"></a>Události
Vlastní události vygenerované [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| počet událostí [0] |integer |100 / ([vzorkování](app-insights-sampling.md) rychlost). Příklad 4 =&gt; 25 %. |
| Název události [0] |řetězec |Název události.  Maximální délka 250. |
| Adresa url pro události [0] |řetězec | |
| události [0] urlData.base |řetězec | |
| události [0] urlData.host |řetězec | |

## <a name="exceptions"></a>Výjimky
Sestavy [výjimky](app-insights-asp-net-exceptions.md) na serveru a v prohlížeči.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| sestavení [0] basicException |řetězec | |
| počet basicException [0] |integer |100 / ([vzorkování](app-insights-sampling.md) rychlost). Příklad 4 =&gt; 25 %. |
| exceptionGroup basicException [0] |řetězec | |
| exceptionType basicException [0] |řetězec | |
| failedUserCodeMethod basicException [0] |řetězec | |
| failedUserCodeAssembly basicException [0] |řetězec | |
| handledAt basicException [0] |řetězec | |
| hasFullStack basicException [0] |Boolean | |
| id basicException [0] |řetězec | |
| Metoda basicException [0] |řetězec | |
| zpráva basicException [0] |řetězec |Zpráva o výjimce. Maximální délka 10 tis. |
| outerExceptionMessage basicException [0] |řetězec | |
| outerExceptionThrownAtAssembly basicException [0] |řetězec | |
| outerExceptionThrownAtMethod basicException [0] |řetězec | |
| outerExceptionType basicException [0] |řetězec | |
| outerId basicException [0] |řetězec | |
| sestavení [0] parsedStack basicException [0] |řetězec | |
| Název souboru parsedStack [0] basicException [0] |řetězec | |
| úroveň parsedStack [0] basicException [0] |integer | |
| basicException [0] [0] parsedStack řádku |integer | |
| Metoda parsedStack [0] basicException [0] |řetězec | |
| Zásobník basicException [0] |řetězec |Maximální délka 10 TIS |
| typeName basicException [0] |řetězec | |

## <a name="trace-messages"></a>Zprávy trasování
Poslal [TrackTrace](app-insights-api-custom-events-metrics.md#tracktrace)a [protokolování adaptéry](app-insights-asp-net-trace-logs.md).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| zprávy [0] Název_protokolovače |řetězec | |
| zprávy [0] Parametry |řetězec | |
| zprávy [0] nezpracovaná |řetězec |Zprávy protokolu, maximální délka 10 tis. |
| úroveň závažnosti zpráva [0] |řetězec | |

## <a name="remote-dependency"></a>Vzdálené závislostí
Odesílá TrackDependency. Umožňuje sestavu výkonu a využití [volání závislosti](app-insights-asp-net-dependencies.md) v serveru a volání AJAX v prohlížeči.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| asynchronní remoteDependency [0] |Boolean | |
| baseName remoteDependency [0] |řetězec | |
| commandName remoteDependency [0] |řetězec |Například "domovskou nebo index" |
| počet remoteDependency [0] |integer |100 / ([vzorkování](app-insights-sampling.md) rychlost). Příklad 4 =&gt; 25 %. |
| dependencyTypeName remoteDependency [0] |řetězec |PROTOKOLU HTTP, SQL... |
| durationMetric.value remoteDependency [0] |číslo |Čas od volání dokončení odpovědi závislostí |
| id remoteDependency [0] |řetězec | |
| Název remoteDependency [0] |řetězec |Adresa URL. Maximální délka 250. |
| resultCode remoteDependency [0] |řetězec |z HTTP závislostí |
| Úspěch remoteDependency [0] |Boolean | |
| Typ remoteDependency [0] |řetězec |Protokolu HTTP, Sql... |
| Adresa url remoteDependency [0] |řetězec |Maximální délka 2000 |
| urlData.base remoteDependency [0] |řetězec |Maximální délka 2000 |
| urlData.hashTag remoteDependency [0] |řetězec | |
| urlData.host remoteDependency [0] |řetězec |Maximální délka 200 |

## <a name="requests"></a>Požadavky
Poslal [TrackRequest](app-insights-api-custom-events-metrics.md#trackrequest). Standardní moduly pomocí tato doba odezvy serveru sestav, měří na serveru.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| počet požadavku [0] |integer |100 / ([vzorkování](app-insights-sampling.md) rychlost). Příklad: 4 =&gt; 25 %. |
| durationMetric.value požadavku [0] |číslo |Čas požadavku přicházejících do odpovědi. 1e7 == hodnotami 1 |
| id požadavku [0] |řetězec |ID operace |
| Název žádosti [0] |řetězec |Základní adresa url + GET nebo POST.  Maximální délka 250 |
| responseCode požadavku [0] |integer |Odpovědi HTTP odeslané do klienta |
| úspěšné žádosti [0] |Boolean |Výchozí == (responseCode &lt; 400) |
| Adresa url požadavku [0] |řetězec |Není včetně hostitele |
| urlData.base požadavku [0] |řetězec | |
| urlData.hashTag požadavku [0] |řetězec | |
| urlData.host požadavku [0] |řetězec | |

## <a name="page-view-performance"></a>Stránka zobrazení výkonu
Posílá prohlížeč. Měří času na zpracování stránky, od uživatele inicializaci žádost zobrazíte kompletní (s výjimkou asynchronní volání AJAX).

Kontext hodnoty zobrazit klientského operačního systému a verze prohlížeče.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| clientProcess.value clientPerformance [0] |integer |Čas od konce přijetí HTML k zobrazení stránky. |
| Název clientPerformance [0] |řetězec | |
| networkConnection.value clientPerformance [0] |integer |Čas potřebný k vytvoření síťového připojení. |
| receiveRequest.value clientPerformance [0] |integer |Čas od konce odesílání požadavku pro příjem kódu HTML v odpovědi. |
| sendRequest.value clientPerformance [0] |integer |Z čas potřebný k odeslání požadavku HTTP. |
| total.value clientPerformance [0] |integer |Čas spuštění odeslat požadavek na zobrazení stránky. |
| Adresa url clientPerformance [0] |řetězec |Adresa URL této žádosti |
| urlData.base clientPerformance [0] |řetězec | |
| urlData.hashTag clientPerformance [0] |řetězec | |
| urlData.host clientPerformance [0] |řetězec | |
| urlData.protocol clientPerformance [0] |řetězec | |

## <a name="page-views"></a>Zobrazení stránek
Poslal trackPageView() nebo [stopTrackPage](app-insights-api-custom-events-metrics.md#page-views)

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| Počet zobrazení [0] |integer |100 / ([vzorkování](app-insights-sampling.md) rychlost). Příklad 4 =&gt; 25 %. |
| zobrazení [0] durationMetric.value |integer |Volitelně můžete nastavit v trackPageView() nebo startTrackPage() - hodnota stopTrackPage(). Není stejný jako clientPerformance hodnoty. |
| Název zobrazení [0] |řetězec |Název stránky.  Maximální délka 250 |
| Adresa url zobrazení [0] |řetězec | |
| zobrazení [0] urlData.base |řetězec | |
| zobrazení [0] urlData.hashTag |řetězec | |
| zobrazení [0] urlData.host |řetězec | |

## <a name="availability"></a>Dostupnost
Sestavy [testy dostupnosti webu](app-insights-monitor-web-app-availability.md).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| availabilityMetric.name dostupnosti [0] |řetězec |dostupnosti |
| availabilityMetric.value dostupnosti [0] |číslo |1.0 nebo 0,0 |
| počet dostupnosti [0] |integer |100 / ([vzorkování](app-insights-sampling.md) rychlost). Příklad 4 =&gt; 25 %. |
| dataSizeMetric.name dostupnosti [0] |řetězec | |
| dataSizeMetric.value dostupnosti [0] |integer | |
| durationMetric.name dostupnosti [0] |řetězec | |
| durationMetric.value dostupnosti [0] |číslo |Doba trvání testu. 1e7 == hodnotami 1 |
| zpráva dostupnosti [0] |řetězec |Selhání diagnostiky |
| výsledek dostupnosti [0] |řetězec |Přijetí nebo vyloučení |
| runLocation dostupnosti [0] |řetězec |Geograficky zdroj žádosti http |
| Název_testu dostupnosti [0] |řetězec | |
| testRunId dostupnosti [0] |řetězec | |
| testTimestamp dostupnosti [0] |řetězec | |

## <a name="metrics"></a>Metriky
Generované TrackMetric().

Metriky hodnota je nalezena v context.custom.metrics[0]

Příklad:

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

## <a name="about-metric-values"></a>O metriky hodnoty
Metriky, v metriky sestavy i jinde, jsou uvedeny se strukturou standardní objektu. Příklad:

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

Aktuálně – Přestože to může změnit v budoucnu – všechny hodnoty nahlásila standardní moduly SDK `count==1` a jenom `name` a `value` pole jsou užitečné. Pouze případ, kdy by být odlišné by, pokud napíšete voláními TrackMetric v můžete nastavit další parametry.

V ostatních polích účelem je umožnit metriky mají agregovat v sadě SDK pro omezení provozu na portál. Například může průměrná několik následných odečty před odesláním všechny metriky sestavy. Potom by vypočítat min, max, směrodatná odchylka a celkovou hodnotu (suma nebo průměr) a nastavte počet počtu odečty reprezentována sestavy.

V tabulkách výš jsme zapomněli málo používané pole count, min, max, stdDev a sampledValue.

Namísto předem prostředku metriky, můžete použít [vzorkování](app-insights-sampling.md) Pokud potřebujete snížit objem telemetrie.

### <a name="durations"></a>Doby trvání
Pokud není uvedeno jinak, jinak jsou reprezentované doby trvání v desetin mikrosekund, tak, aby 10000000.0 znamená 1 sekunda.

## <a name="see-also"></a>Další informace najdete v tématech
* [Application Insights](app-insights-overview.md)
* [Průběžné exportu](app-insights-export-telemetry.md)
* [Ukázky kódu](app-insights-export-telemetry.md#code-samples)
