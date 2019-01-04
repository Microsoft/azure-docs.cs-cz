---
title: Azure Application Insights datový Model | Dokumentace Microsoftu
description: Popisuje vlastnosti exportu ze průběžný export ve formátu JSON a použít jako filtry.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: mbullwin
ms.openlocfilehash: c906f313ae367fcc660d1ce1df0b3d667c7f04a3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016100"
---
# <a name="application-insights-export-data-model"></a>Application Insights Export datového modelu
V této tabulce jsou uvedeny vlastnosti objektu telemetrická data odesílaná z [Application Insights](../../application-insights/app-insights-overview.md) sady SDK k portálu.
Zobrazí se vám tyto vlastnosti v datovým výstupem z [průběžný Export](export-telemetry.md).
Jsou také uvedeny v filtry vlastností v [Průzkumník metrik](../../azure-monitor/app/metrics-explorer.md) a [diagnostické vyhledávání](../../azure-monitor/app/diagnostic-search.md).

Odkazuje na mějte na paměti:

* `[0]` v těchto tabulkách označuje bod na cestě, kde je třeba vložit indexu; ale není to vždycky 0.
* Dob trvání se v desetiny úrovni mikrosekund, takže 10000000 == 1 sekundu.
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
Všechny typy telemetrie doplňují oddíl kontextu. Všechna tato pole jsou přenášeny se každý datový bod.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| Context.Custom.Dimensions [0] |[] č. objekt |Páry klíč hodnota řetězce nastavit parametr vlastní vlastnosti. Maximální délka klíče 100 hodnot maximální délky 1024. Více než 100 jedinečné hodnoty vlastnosti lze prohledávat, ale nelze použít pro segmentace. 200 maximální počet klíčů na Instrumentační klíč. |
| Context.Custom.Metrics [0] |[] č. objekt |Nastavit tak, že parametr vlastní měření a TrackMetrics páry klíč hodnota. Maximální délka klíče 100, mohou být číselné hodnoty. |
| context.data.eventTime |řetězec |UTC |
| context.data.isSynthetic |Boolean |Zdá se, že žádost o pocházejí z bot nebo webový test. |
| context.data.samplingRate |číslo |Procento telemetrii generovanou sady SDK, která je odeslána na portál. V rozsahu od 0,0 100.0. |
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
| Context.Location |objekt |Odvozený od clientip. |
| Context.location.City |řetězec |Odvozený od clientip, pokud jsou známé |
| Context.location.ClientIP |řetězec |Poslední osmiúhelníkem jsou anonymní na hodnotu 0. |
| Context.location.Continent |řetězec | |
| Context.location.Country |řetězec | |
| Context.location.Province |řetězec |Stát nebo kraj |
| Context.Operation.ID |řetězec |Položky, které mají stejné id operace se zobrazují jako související položky na portálu. Obvykle id požadavku. |
| Context.Operation.Name |řetězec |Název adresy URL nebo žádosti |
| context.operation.parentId |řetězec |Umožňuje vnořené související položky. |
| Context.Session.ID |řetězec |ID skupiny operací z jednoho zdroje. 30 minut bez operace signalizuje ukončení relace. |
| context.session.isFirst |Boolean | |
| context.user.accountAcquisitionDate |řetězec | |
| context.user.anonAcquisitionDate |řetězec | |
| context.user.anonId |řetězec | |
| context.user.authAcquisitionDate |řetězec |[Ověřený uživatel](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated |Boolean | |
| internal.data.documentVersion |řetězec | |
| internal.data.ID |řetězec | Jedinečný identifikátor, který je přidělen položky se ingestuje do služby Application Insights |

## <a name="events"></a>Události
Vlastní události generované modulem [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| počet událostí [0] |integer |100 / ([vzorkování](../../azure-monitor/app/sampling.md) rychlost). Příklad 4 =&gt; 25 %. |
| Název události [0] |řetězec |Název události.  Maximální délka 250. |
| Adresa url pro události [0] |řetězec | |
| události [0] urlData.base |řetězec | |
| události [0] urlData.host |řetězec | |

## <a name="exceptions"></a>Výjimky
Sestavy [výjimky](../../azure-monitor/app/asp-net-exceptions.md) na serveru a v prohlížeči.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| sestavení basicException [0] |řetězec | |
| počet basicException [0] |integer |100 / ([vzorkování](../../azure-monitor/app/sampling.md) rychlost). Příklad 4 =&gt; 25 %. |
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
| basicException [0] [0] parsedStack sestavení |řetězec | |
| Název souboru parsedStack [0] [0] basicException |řetězec | |
| úroveň parsedStack [0] [0] basicException |integer | |
| basicException [0] [0] parsedStack řádku |integer | |
| basicException [0] [0] parsedStack – metoda |řetězec | |
| Zásobník basicException [0] |řetězec |Maximální délka 10 tisíc |
| typeName basicException [0] |řetězec | |

## <a name="trace-messages"></a>Zprávy trasování
Odeslaný [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)a [adaptéry protokolování](../../azure-monitor/app/asp-net-trace-logs.md).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| Název_protokolovače zprávy [0] |řetězec | |
| parametry zpráv [0] |řetězec | |
| nezpracované zprávy [0] |řetězec |Zpráva protokolu 10 tisíc znaků. |
| severityLevel zprávy [0] |řetězec | |

## <a name="remote-dependency"></a>Vzdálené závislosti
Odeslaný TrackDependency. Pro sestavu výkonu a využití [volání závislostí](../../azure-monitor/app/asp-net-dependencies.md) na serveru a volání AJAX v prohlížeči.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| asynchronní remoteDependency [0] |Boolean | |
| baseName remoteDependency [0] |řetězec | |
| commandName remoteDependency [0] |řetězec |Například "home/index" |
| počet remoteDependency [0] |integer |100 / ([vzorkování](../../azure-monitor/app/sampling.md) rychlost). Příklad 4 =&gt; 25 %. |
| dependencyTypeName remoteDependency [0] |řetězec |HTTP, SQL... |
| durationMetric.value remoteDependency [0] |číslo |Čas z volání do konce odpovědi závislostí |
| id remoteDependency [0] |řetězec | |
| Název remoteDependency [0] |řetězec |Adresa URL. Maximální délka 250. |
| Kód výsledku remoteDependency [0] |řetězec |ze závislostí protokolu HTTP |
| Úspěch remoteDependency [0] |Boolean | |
| Typ remoteDependency [0] |řetězec |Http, Sql... |
| Adresa url remoteDependency [0] |řetězec |Maximální délka 2000 |
| urlData.base remoteDependency [0] |řetězec |Maximální délka 2000 |
| urlData.hashTag remoteDependency [0] |řetězec | |
| urlData.host remoteDependency [0] |řetězec |Maximální délka 200 |

## <a name="requests"></a>Požadavky
Odeslaný [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Standardní moduly využit k doba odezvy serveru sestav, měří na serveru.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| Počet požadavků [0] |integer |100 / ([vzorkování](../../azure-monitor/app/sampling.md) rychlost). Příklad: 4 =&gt; 25 %. |
| žádost o [0] durationMetric.value |číslo |Doba od žádosti přicházející do odpovědi. 1e7 == 1s |
| id požadavku [0] |řetězec |ID operace |
| Název žádosti [0] |řetězec |Základ adresy url + GET/POST.  Maximální délka 250 |
| žádost o [0] responseCode |integer |Odpovědi HTTP odeslané do klienta |
| Úspěch požadavku [0] |Boolean |Výchozí == (responseCode &lt; 400) |
| Adresa url požadavku [0] |řetězec |Nezahrnuje hostitele |
| žádost o [0] urlData.base |řetězec | |
| žádost o [0] urlData.hashTag |řetězec | |
| žádost o [0] urlData.host |řetězec | |

## <a name="page-view-performance"></a>Výkon zobrazení stránky
Odeslání v prohlížeči. Měří čas ke zpracování stránky, od uživatele inicializaci žádosti zobrazíte kompletní (s výjimkou asynchronní volání jazyka AJAX).

Kontext hodnoty zobrazit klientský operační systém a verze prohlížeče.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| clientProcess.value clientPerformance [0] |integer |Čas od konce příjem kód HTML pro zobrazení stránky. |
| Název clientPerformance [0] |řetězec | |
| networkConnection.value clientPerformance [0] |integer |Čas potřebný k navázání připojení k síti. |
| receiveRequest.value clientPerformance [0] |integer |Čas od konce odesílání požadavku na příjem HTML v odpovědi. |
| sendRequest.value clientPerformance [0] |integer |Z čas potřebný k odeslání požadavku HTTP. |
| total.value clientPerformance [0] |integer |Čas spuštění odeslat požadavek na zobrazení stránky. |
| Adresa url clientPerformance [0] |řetězec |Adresa URL této žádosti |
| urlData.base clientPerformance [0] |řetězec | |
| urlData.hashTag clientPerformance [0] |řetězec | |
| urlData.host clientPerformance [0] |řetězec | |
| urlData.protocol clientPerformance [0] |řetězec | |

## <a name="page-views"></a>Zobrazení stránek
Odeslaný trackPageView() nebo [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| Počet zobrazení [0] |integer |100 / ([vzorkování](../../azure-monitor/app/sampling.md) rychlost). Příklad 4 =&gt; 25 %. |
| zobrazení [0] durationMetric.value |integer |Volitelně můžete nastavit v trackPageView() nebo startTrackPage() – hodnota stopTrackPage(). Není stejný jako clientPerformance hodnoty. |
| Název zobrazení [0] |řetězec |Název stránky  Maximální délka 250 |
| Adresa url zobrazení [0] |řetězec | |
| zobrazení [0] urlData.base |řetězec | |
| zobrazení [0] urlData.hashTag |řetězec | |
| zobrazení [0] urlData.host |řetězec | |

## <a name="availability"></a>Dostupnost
Sestavy [testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| availabilityMetric.name dostupnost [0] |řetězec |dostupnosti |
| availabilityMetric.value dostupnost [0] |číslo |1.0 nebo 0,0 |
| počet dostupnost [0] |integer |100 / ([vzorkování](../../azure-monitor/app/sampling.md) rychlost). Příklad 4 =&gt; 25 %. |
| dataSizeMetric.name dostupnost [0] |řetězec | |
| dataSizeMetric.value dostupnost [0] |integer | |
| durationMetric.name dostupnost [0] |řetězec | |
| durationMetric.value dostupnost [0] |číslo |Doba trvání testu. 1e7 == 1s |
| zpráva o dostupnosti [0] |řetězec |Diagnostika chybu |
| výsledek dostupnosti [0] |řetězec |Úspěšný/selhání |
| runLocation dostupnost [0] |řetězec |Geograficky příčiny No http |
| Název_testu dostupnost [0] |řetězec | |
| ID testovacího běhu dostupnost [0] |řetězec | |
| testTimestamp dostupnost [0] |řetězec | |

## <a name="metrics"></a>Metriky
Generované metody TrackMetric().

Hodnota metriky je součástí context.custom.metrics[0]

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

## <a name="about-metric-values"></a>O hodnoty metrik
Hodnoty metrik, jak v sestavách metriky a jinde, označené pomocí standardní objektovou strukturu. Příklad:

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

Aktuálně – i když to může změnit v budoucnu – všechny hodnoty nahlásila standardní moduly SDK `count==1` a jenom `name` a `value` pole jsou užitečná. Jediný případ, kde by se použily různé by, pokud napíšete TrackMetric volání v můžete nastavit další parametry.

Účelem další pole je umožnit metriky, které se dají agregovat v sadě SDK pro omezení provozu na portál. Může například průměrná několik po sobě jdoucích odečty před odesláním každé zprávu metriky. Pak by výpočet min, max, směrodatná odchylka a agregované hodnoty (součtu nebo průměru) a nastavte počet na počet čtení reprezentovaný touto sestavou.

V tabulkách výše jsme zapomněli zřídka používaná pole count, min, max, stdDev a sampledValue.

Namísto předem agregovat metriky, můžete použít [vzorkování](../../azure-monitor/app/sampling.md) potřebujete snížit objem telemetrických dat.

### <a name="durations"></a>Doba trvání
Pokud není uvedeno jinak, jinak jsou reprezentovány doby trvání v desetiny úrovni mikrosekund tak, aby 10000000.0 znamená, že jedna sekunda.

## <a name="see-also"></a>Další informace najdete v tématech
* [Application Insights](../../application-insights/app-insights-overview.md)
* [Průběžný Export.](export-telemetry.md)
* [Ukázky kódu](export-telemetry.md#code-samples)
