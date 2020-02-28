---
title: Datový model Azure Application Insights | Microsoft Docs
description: Popisuje vlastnosti exportované z průběžného exportu ve formátu JSON a slouží jako filtry.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: e4dd2310169476e54c06083fee11b2e4cccecd8d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663871"
---
# <a name="application-insights-export-data-model"></a>Application Insights Exportovat datový model
Tato tabulka obsahuje seznam vlastností telemetrie odeslaných z [Application Insights](../../azure-monitor/app/app-insights-overview.md) sad SDK na portál.
Tyto vlastnosti se zobrazí ve výstupu dat z [průběžného exportu](export-telemetry.md).
Zobrazují se také v filtrech vlastností v [Průzkumníkovi metrik](../../azure-monitor/app/metrics-explorer.md) a [diagnostickém vyhledávání](../../azure-monitor/app/diagnostic-search.md).

Odkazuje na mějte na paměti:

* `[0]` v těchto tabulkách označuje bod v cestě, kam je nutné vložit index. ale ne vždycky 0.
* Doby trvání jsou desetiny sekundy, takže 10000000 = = 1 sekunda.
* Data a časy jsou UTC a jsou uvedené ve formátu ISO `yyyy-MM-DDThh:mm:ss.sssZ`


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
Všechny typy telemetrie jsou doprovázeny kontextovým oddílem. Všechna tato pole nejsou přenášena s každým datovým bodem.

| Cesta | Typ | Poznámky: |
| --- | --- | --- |
| context.custom.dimensions [0] |objekt [] |Páry řetězců klíč-hodnota nastavené parametrem Custom Properties. Maximální délka klíče 100, maximální délka hodnoty je 1024. Více než 100 jedinečných hodnot – vlastnost lze vyhledat, ale nelze ji použít pro segmentaci. Maximální počet 200 klíčů na ikey |
| context.custom.metrics [0] |objekt [] |Páry klíč-hodnota nastavené vlastním parametrem měření a hodnotou TrackMetrics. Maximální délka klíče 100, hodnoty můžou být číselné. |
| context.data.eventTime |string |UTC |
| context.data.isSynthetic |Boolean |Požadavek se zdá, že robot nebo webový test pochází. |
| context.data.samplingRate |číslo |Procento telemetrii generovanou sady SDK, která je odeslána na portál. V rozsahu od 0,0 100.0. |
| context.device |objekt |Klientské zařízení |
| context.device.browser |string |IE, Chrome,... |
| context.device.browserVersion |string |Chrome 48,0,... |
| context.device.deviceModel |string | |
| context.device.deviceName |string | |
| context.device.id |string | |
| Context. Device. locale |string |en-GB, de-DE,... |
| context.device.network |string | |
| context.device.oemName |string | |
| context.device.os |string | |
| context.device.osVersion |string |Hostitelský operační systém |
| context.device.roleInstance |string |ID hostitelského serveru |
| context.device.roleName |string | |
| Context. Device. screenResolution |string | |
| context.device.type |string |POČÍTAČ, prohlížeč,... |
| Context. Location |objekt |Odvozeno z IP adresa klienta. |
| Context. Location. City |string |Odvozeno z IP adresa klienta, pokud je známo |
| context.location.clientip |string |Poslední osmiúhelníkem je Anonyme na hodnotu 0. |
| Context. Location. kontinent |string | |
| Context. Location. Country |string | |
| Context. Location. provincie |string |Stát nebo provincie |
| context.operation.id |string |Položky, které mají stejné ID operace, se zobrazují jako související položky na portálu. Obvykle se jedná o ID žádosti. |
| context.operation.name |string |Adresa URL nebo název žádosti |
| context.operation.parentId |string |Povoluje vnořené související položky. |
| context.session.id |string |ID skupiny operací ze stejného zdroje Doba 30 minut bez operace signalizuje ukončení relace. |
| context.session.isFirst |Boolean | |
| context.user.accountAcquisitionDate |string | |
| context.user.accountId |string | |
| context.user.anonAcquisitionDate |string | |
| context.user.anonId |string | |
| context.user.authAcquisitionDate |string |[Ověřený uživatel](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |string | |
| context.user.isAuthenticated |Boolean | |
| context.user.storeRegion |string | |
| internal.data.documentVersion |string | |
| internal.data.id |string | Jedinečné ID, které je přiřazeno při ingestování položky Application Insights |

## <a name="events"></a>Události
Vlastní události generované [TrackEvent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Cesta | Typ | Poznámky: |
| --- | --- | --- |
| počet událostí [0] |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Například 4 =&gt; 25%. |
| název události [0] |string |Název události  Maximální délka 250. |
| Adresa URL události [0] |string | |
| událost [0] urlData. Base |string | |
| událost [0] urlData. Host |string | |

## <a name="exceptions"></a>Výjimky
Oznamuje [výjimky](../../azure-monitor/app/asp-net-exceptions.md) na serveru a v prohlížeči.

| Cesta | Typ | Poznámky: |
| --- | --- | --- |
| basicException [0] sestavení |string | |
| basicException [0] počet |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Například 4 =&gt; 25%. |
| basicException [0] exceptionGroup |string | |
| basicException [0] Typvýjimky |string | |
| basicException [0] failedUserCodeMethod |string | |
| basicException [0] failedUserCodeAssembly |string | |
| basicException [0] handledAt |string | |
| basicException [0] hasFullStack |Boolean | |
| basicException [0] ID |string | |
| basicException [0] – metoda |string | |
| Zpráva basicException [0] |string |Zpráva výjimky Maximální délka 10 000. |
| basicException [0] outerExceptionMessage |string | |
| basicException [0] outerExceptionThrownAtAssembly |string | |
| basicException [0] outerExceptionThrownAtMethod |string | |
| basicException [0] outerExceptionType |string | |
| basicException [0] outerId |string | |
| basicException [0] parsedStack [0] sestavení |string | |
| basicException [0] parsedStack [0] fileName |string | |
| basicException [0] parsedStack [0] úroveň |celé číslo | |
| basicException [0] parsedStack [0] řádek |celé číslo | |
| basicException [0] parsedStack [0] – metoda |string | |
| basicException [0] zásobník |string |Maximální délka 10 000 |
| basicException [0] typeName |string | |

## <a name="trace-messages"></a>Trasovat zprávy
Odesílá [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)a [adaptéry protokolování](../../azure-monitor/app/asp-net-trace-logs.md).

| Cesta | Typ | Poznámky: |
| --- | --- | --- |
| zpráva [0] protokolovacího nástroje |string | |
| zprávy [0] parametry |string | |
| zpráva [0] RAW |string |Zpráva protokolu, maximální délka 10 000. |
| zpráva [0] severityLevel |string | |

## <a name="remote-dependency"></a>Vzdálená závislost
Odesílá TrackDependency. Slouží k hlášení výkonu a využití [volání závislostí](../../azure-monitor/app/asp-net-dependencies.md) na serveru a volání AJAX v prohlížeči.

| Cesta | Typ | Poznámky: |
| --- | --- | --- |
| remoteDependency [0] asynchronní |Boolean | |
| remoteDependency [0] – základ |string | |
| remoteDependency [0] příkaz |string |Například "domů/index" |
| remoteDependency [0] počet |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Například 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName |string |HTTP, SQL, ... |
| remoteDependency [0] durationMetric. Value |číslo |Čas od volání k dokončení odpovědi podle závislosti |
| remoteDependency [0] ID |string | |
| remoteDependency [0] název |string |Adresa URL. Maximální délka 250. |
| remoteDependency [0] resultCode |string |ze závislosti protokolu HTTP |
| remoteDependency [0] úspěch |Boolean | |
| remoteDependency [0] typ |string |Http,,... SQL |
| remoteDependency [0] Adresa URL |string |Maximální délka 2000 |
| remoteDependency [0] urlData. Base |string |Maximální délka 2000 |
| remoteDependency [0] urlData. hashTag |string | |
| remoteDependency [0] urlData. Host |string |Maximální délka 200 |

## <a name="requests"></a>Požadavky
Odesílá [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Standardní moduly tuto metodu používají k hlášení doby odezvy serveru měřenou na serveru.

| Cesta | Typ | Poznámky: |
| --- | --- | --- |
| požadavek [0] počet |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Příklad: 4 =&gt; 25%. |
| požadavek [0] durationMetric. Value |číslo |Čas od žádosti přicházející do odpovědi 1e7 == 1s |
| požadavek [0] ID |string |ID operace |
| žádost [0] název |string |Základ pro GET/POST + URL  Maximální délka 250 |
| žádost [0] responseCode |celé číslo |Odpovědi HTTP odeslané do klienta |
| žádost [0] byla úspěšná. |Boolean |Výchozí = = (responseCode &lt; 400) |
| žádost [0] Adresa URL |string |Nezahrnuje hostitel |
| požadavek [0] urlData. Base |string | |
| požadavek [0] urlData. hashTag |string | |
| požadavek [0] urlData. Host |string | |

## <a name="page-view-performance"></a>Výkon zobrazení stránky
Odesílá se v prohlížeči. Měří čas na zpracování stránky, od uživatele iniciující požadavek na zobrazení úplného (kromě asynchronních volání AJAX).

Kontextové hodnoty znázorňují klientský operační systém a verzi prohlížeče.

| Cesta | Typ | Poznámky: |
| --- | --- | --- |
| clientPerformance [0] clientProcess. Value |celé číslo |Doba od konce příjmu HTML po zobrazení stránky. |
| clientPerformance [0] název |string | |
| clientPerformance [0] networkConnection. Value |celé číslo |Doba potřebná k navázání připojení k síti |
| clientPerformance [0] receiveRequest. Value |celé číslo |Doba od konce odeslání žádosti o přijetí kódu HTML v odpovědi. |
| clientPerformance [0] sendRequest. Value |celé číslo |Doba od pořízení pro odeslání požadavku HTTP. |
| clientPerformance [0] Celková hodnota |celé číslo |Čas od zahájení odesílání žádosti o zobrazení stránky |
| clientPerformance [0] Adresa URL |string |Adresa URL této žádosti |
| clientPerformance [0] urlData. Base |string | |
| clientPerformance [0] urlData. hashTag |string | |
| clientPerformance [0] urlData. Host |string | |
| clientPerformance [0] urlData. Protocol |string | |

## <a name="page-views"></a>Zobrazení stránek
Odesílá se pomocí trackPageView () nebo [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Cesta | Typ | Poznámky: |
| --- | --- | --- |
| Zobrazit [0] počet |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Například 4 =&gt; 25%. |
| Zobrazit [0] durationMetric. Value |celé číslo |Hodnota volitelně nastavena v trackPageView () nebo pomocí startTrackPage ()-stopTrackPage (). Nejedná se o stejné hodnoty jako clientPerformance hodnoty. |
| Zobrazit [0] název |string |Nadpis stránky  Maximální délka 250 |
| Zobrazit adresu URL [0] |string | |
| zobrazení [0] urlData. Base |string | |
| zobrazení [0] urlData. hashTag |string | |
| Zobrazit [0] urlData. Host |string | |

## <a name="availability"></a>Dostupnost
Oznamuje [webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md).

| Cesta | Typ | Poznámky: |
| --- | --- | --- |
| dostupnost [0] availabilityMetric.name |string |dostupnost |
| dostupnost [0] availabilityMetric. Value |číslo |1.0 nebo 0,0 |
| počet dostupnosti [0] |celé číslo |100/([vzorkovací](../../azure-monitor/app/sampling.md) frekvence). Například 4 =&gt; 25%. |
| dostupnost [0] dataSizeMetric.name |string | |
| dostupnost [0] dataSizeMetric. Value |celé číslo | |
| dostupnost [0] durationMetric.name |string | |
| dostupnost [0] durationMetric. Value |číslo |Doba trvání testu. 1e7==1s |
| Zpráva o dostupnosti [0] |string |Diagnostika selhání |
| výsledek dostupnosti [0] |string |Úspěch/selhání |
| dostupnost [0] runLocation |string |Geografický zdroj požadavku HTTP REQ |
| dostupnost [0] test |string | |
| dostupnost [0] běhu |string | |
| dostupnost [0] testTimestamp |string | |

## <a name="metrics"></a>Metriky
Vygenerováno pomocí TrackMetric ().

Hodnota metriky se nachází v kontextu. Custom. Metrics [0]

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

## <a name="about-metric-values"></a>O hodnotách metriky
Hodnoty metriky v sestavách metriky i jinde jsou hlášeny se standardní strukturou objektů. Příklad:

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

V současné době se může v budoucnu změnit na všechny hodnoty hlášené ze standardních modulů sady SDK, `count==1` a jsou užitečné pouze pole `name` a `value`. Jediný případ, kdy by byl jiný, by byl při psaní vlastních volání TrackMetric, ve kterých jste nastavili další parametry.

Účelem ostatních polí je umožnění agregace metrik v sadě SDK za účelem snížení provozu na portál. Před odesláním každé sestavy metriky můžete například vypočítat průměrně několik následných čtení. Pak byste měli vypočítat minimální, maximální, směrodatnou odchylku a agregovanou hodnotu (součet nebo průměr) a nastavit počet na počet čtení reprezentovaných sestavou.

V tabulkách výše jsme vynechali počet zřídka používaných polí: Count, min, Max, stdDev a sampledValue.

Místo předagregačních metrik můžete použít [vzorkování](../../azure-monitor/app/sampling.md) , pokud potřebujete snížit objem telemetrie.

### <a name="durations"></a>Dob trvání
S výjimkou případů, kdy je uvedeno jinak, jsou doby trvání vyjádřeny desáty sekundy, takže 10000000,0 znamená 1 sekundu.

## <a name="see-also"></a>Viz také
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Průběžný export](export-telemetry.md)
* [Ukázky kódu](export-telemetry.md#code-samples)
