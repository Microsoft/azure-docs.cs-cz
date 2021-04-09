---
title: Datový model Azure Application Insights | Microsoft Docs
description: Popisuje vlastnosti exportované z průběžného exportu ve formátu JSON a slouží jako filtry.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: b4609d54c1c3c33a654dd58a3bceaca4974fda15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584225"
---
# <a name="application-insights-export-data-model"></a>Application Insights Exportovat datový model
Tato tabulka obsahuje seznam vlastností telemetrie odeslaných z [Application Insights](./app-insights-overview.md) sad SDK na portál.
Tyto vlastnosti se zobrazí ve výstupu dat z [průběžného exportu](export-telemetry.md).
Zobrazují se také v filtrech vlastností v [Průzkumníkovi metrik](../essentials/metrics-charts.md) a [diagnostickém vyhledávání](./diagnostic-search.md).

Ukazuje na poznámku:

* `[0]` v těchto tabulkách označuje bod v cestě, kam je nutné vložit index. ale ne vždycky 0.
* Doby trvání jsou desetiny sekundy, takže 10000000 = = 1 sekunda.
* Data a časy jsou UTC a jsou uvedené ve formátu ISO. `yyyy-MM-DDThh:mm:ss.sssZ`

## <a name="example"></a>Příklad

```json
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
```

## <a name="context"></a>Kontext
Všechny typy telemetrie jsou doprovázeny kontextovým oddílem. Všechna tato pole nejsou přenášena s každým datovým bodem.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| Context. Custom. Dimensions [0] |objekt [] |Páry řetězců klíč-hodnota nastavené parametrem Custom Properties. Maximální délka klíče 100, maximální délka hodnoty je 1024. Více než 100 jedinečných hodnot – vlastnost lze vyhledat, ale nelze ji použít pro segmentaci. Maximální počet 200 klíčů na ikey |
| Context. Custom. Metrics [0] |objekt [] |Páry klíč-hodnota nastavené vlastním parametrem měření a hodnotou TrackMetrics. Maximální délka klíče 100, hodnoty můžou být číselné. |
| Context. data. čas události |řetězec |UTC |
| Context. data. syntetická |boolean |Požadavek se zdá, že robot nebo webový test pochází. |
| Context. data. samplingRate |číslo |Procento telemetrie vygenerované sadou SDK, která je odeslána na portál. Rozsah 0,0 – 100,0. |
| kontext. zařízení |object |Klientské zařízení |
| Context. Device. browser |řetězec |IE, Chrome,... |
| Context. Device. browserVersion |řetězec |Chrome 48,0,... |
| Context. Device. deviceModel |řetězec | |
| Context. Device. název_zařízení |řetězec | |
| context.device.id |řetězec | |
| Context. Device. locale |řetězec |en-GB, de-DE,... |
| Context. Device. Network |řetězec | |
| Context. Device. oemName |řetězec | |
| Context. Device. OS |řetězec | |
| Context. Device. osVersion |řetězec |Hostitelský operační systém |
| Context. Device. roleInstance |řetězec |ID hostitelského serveru |
| Context. Device. roleName |řetězec | |
| Context. Device. screenResolution |řetězec | |
| Context. Device. Type |řetězec |POČÍTAČ, prohlížeč,... |
| Context. Location |object |Odvozeno z `clientip` . |
| Context. Location. City |řetězec |Odvozeno z `clientip` , je-li známo |
| Context. Location. IP adresa klienta |řetězec |Poslední osmiúhelníkem je Anonyme na hodnotu 0. |
| Context. Location. kontinent |řetězec | |
| Context. Location. Country |řetězec | |
| Context. Location. provincie |řetězec |Stát nebo provincie |
| context.operation.id |řetězec |Položky, které jsou stejné, `operation id` se zobrazí jako související položky na portálu. Obvykle je `request id` . |
| context.operation.name |řetězec |Adresa URL nebo název žádosti |
| Context. Operation. parentId |řetězec |Povoluje vnořené související položky. |
| context.session.id |řetězec |`Id` skupiny operací ze stejného zdroje. Doba 30 minut bez operace signalizuje ukončení relace. |
| Context. Session.-First |boolean | |
| Context. User. accountAcquisitionDate |řetězec | |
| Context. User. accountId |řetězec | |
| Context. User. anonAcquisitionDate |řetězec | |
| Context. User. anonId |řetězec | |
| Context. User. authAcquisitionDate |řetězec |[Ověřený uživatel](./api-custom-events-metrics.md#authenticated-users) |
| Context. User. authId |řetězec | |
| Context. User.-Authenticated |boolean | |
| Context. User. storeRegion |řetězec | |
| internal.data.documentVersion |řetězec | |
| internal.data.id |řetězec | `Unique id` který je přiřazen při ingestování položky Application Insights |

## <a name="events"></a>Události
Vlastní události generované [TrackEvent ()](./api-custom-events-metrics.md#trackevent).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| počet událostí [0] |integer |100/([vzorkovací](./sampling.md) frekvence). Například 4 = &gt; 25%. |
| název události [0] |řetězec |Název události  Maximální délka 250. |
| Adresa URL události [0] |řetězec | |
| událost [0] urlData. Base |řetězec | |
| událost [0] urlData. Host |řetězec | |

## <a name="exceptions"></a>Výjimky
Oznamuje [výjimky](./asp-net-exceptions.md) na serveru a v prohlížeči.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| basicException [0] sestavení |řetězec | |
| basicException [0] počet |integer |100/([vzorkovací](./sampling.md) frekvence). Například 4 = &gt; 25%. |
| basicException [0] výjimka |řetězec | |
| basicException [0] Typvýjimky |řetězec | |
| basicException [0] failedUserCodeMethod |řetězec | |
| basicException [0] failedUserCodeAssembly |řetězec | |
| basicException [0] handledAt |řetězec | |
| basicException [0] hasFullStack |boolean | |
| basicException [0] `id` |řetězec | |
| basicException [0] – metoda |řetězec | |
| Zpráva basicException [0] |řetězec |Zpráva výjimky Maximální délka 10 000. |
| basicException [0] outerExceptionMessage |řetězec | |
| basicException [0] outerExceptionThrownAtAssembly |řetězec | |
| basicException [0] outerExceptionThrownAtMethod |řetězec | |
| basicException [0] outerExceptionType |řetězec | |
| basicException [0] outerId |řetězec | |
| basicException [0] parsedStack [0] sestavení |řetězec | |
| basicException [0] parsedStack [0] fileName |řetězec | |
| basicException [0] parsedStack [0] úroveň |integer | |
| basicException [0] parsedStack [0] řádek |integer | |
| basicException [0] parsedStack [0] – metoda |řetězec | |
| basicException [0] zásobník |řetězec |Maximální délka 10 000 |
| basicException [0] typeName |řetězec | |

## <a name="trace-messages"></a>Trasovat zprávy
Odesílá [TrackTrace](./api-custom-events-metrics.md#tracktrace)a [adaptéry protokolování](./asp-net-trace-logs.md).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| zpráva [0] protokolovacího nástroje |řetězec | |
| zprávy [0] parametry |řetězec | |
| zpráva [0] RAW |řetězec |Zpráva protokolu, maximální délka 10 000. |
| zpráva [0] severityLevel |řetězec | |

## <a name="remote-dependency"></a>Vzdálená závislost
Odesílá TrackDependency. Slouží k hlášení výkonu a využití [volání závislostí](./asp-net-dependencies.md) na serveru a volání AJAX v prohlížeči.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| remoteDependency [0] asynchronní |boolean | |
| remoteDependency [0] – základ |řetězec | |
| remoteDependency [0] příkaz |řetězec |Například "domů/index" |
| remoteDependency [0] počet |integer |100/([vzorkovací](./sampling.md) frekvence). Například 4 = &gt; 25%. |
| remoteDependency [0] dependencyTypeName |řetězec |HTTP, SQL,... |
| remoteDependency [0] durationMetric. Value |číslo |Čas od volání k dokončení odpovědi podle závislosti |
| remoteDependency [0] `id` |řetězec | |
| remoteDependency [0] název |řetězec |Adresa URL. Maximální délka 250. |
| remoteDependency [0] resultCode |řetězec |ze závislosti protokolu HTTP |
| remoteDependency [0] úspěch |boolean | |
| remoteDependency [0] typ |řetězec |Http,,... SQL |
| remoteDependency [0] Adresa URL |řetězec |Maximální délka 2000 |
| remoteDependency [0] urlData. Base |řetězec |Maximální délka 2000 |
| remoteDependency [0] urlData. hashTag |řetězec | |
| remoteDependency [0] urlData. Host |řetězec |Maximální délka 200 |

## <a name="requests"></a>Žádosti
Odesílá [TrackRequest](./api-custom-events-metrics.md#trackrequest). Standardní moduly tuto metodu používají k hlášení doby odezvy serveru měřenou na serveru.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| požadavek [0] počet |integer |100/([vzorkovací](./sampling.md) frekvence). Příklad: 4 = &gt; 25%. |
| požadavek [0] durationMetric. Value |číslo |Čas od žádosti přicházející do odpovědi 1E7 = = 1 |
| žádost [0] `id` |řetězec |`Operation id` |
| žádost [0] název |řetězec |Základ pro GET/POST + URL  Maximální délka 250 |
| žádost [0] responseCode |integer |Odpověď HTTP se odeslala klientovi. |
| žádost [0] byla úspěšná. |boolean |Výchozí nastavení = = (responseCode &lt; 400) |
| žádost [0] Adresa URL |řetězec |Nezahrnuje hostitel |
| požadavek [0] urlData. Base |řetězec | |
| požadavek [0] urlData. hashTag |řetězec | |
| požadavek [0] urlData. Host |řetězec | |

## <a name="page-view-performance"></a>Výkon zobrazení stránky
Odesílá se v prohlížeči. Měří čas na zpracování stránky, od uživatele iniciující požadavek na zobrazení úplného (kromě asynchronních volání AJAX).

Kontextové hodnoty znázorňují klientský operační systém a verzi prohlížeče.

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| clientPerformance [0] clientProcess. Value |integer |Doba od konce příjmu HTML po zobrazení stránky. |
| clientPerformance [0] název |řetězec | |
| clientPerformance [0] networkConnection. Value |integer |Doba potřebná k navázání připojení k síti |
| clientPerformance [0] receiveRequest. Value |integer |Doba od konce odeslání žádosti o přijetí kódu HTML v odpovědi. |
| clientPerformance [0] sendRequest. Value |integer |Doba od pořízení pro odeslání požadavku HTTP. |
| clientPerformance [0] Celková hodnota |integer |Čas od zahájení odesílání žádosti o zobrazení stránky |
| clientPerformance [0] Adresa URL |řetězec |Adresa URL této žádosti |
| clientPerformance [0] urlData. Base |řetězec | |
| clientPerformance [0] urlData. hashTag |řetězec | |
| clientPerformance [0] urlData. Host |řetězec | |
| clientPerformance [0] urlData. Protocol |řetězec | |

## <a name="page-views"></a>Page Views
Odesílá se pomocí trackPageView () nebo [stopTrackPage](./api-custom-events-metrics.md#page-views)

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| Zobrazit [0] počet |integer |100/([vzorkovací](./sampling.md) frekvence). Například 4 = &gt; 25%. |
| Zobrazit [0] durationMetric. Value |integer |Hodnota volitelně nastavena v trackPageView () nebo pomocí startTrackPage ()-stopTrackPage (). Nejedná se o stejné hodnoty jako clientPerformance hodnoty. |
| Zobrazit [0] název |řetězec |Nadpis stránky  Maximální délka 250 |
| Zobrazit adresu URL [0] |řetězec | |
| zobrazení [0] urlData. Base |řetězec | |
| zobrazení [0] urlData. hashTag |řetězec | |
| Zobrazit [0] urlData. Host |řetězec | |

## <a name="availability"></a>Dostupnost
Oznamuje [webové testy dostupnosti](./monitor-web-app-availability.md).

| Cesta | Typ | Poznámky |
| --- | --- | --- |
| dostupnost [0] availabilityMetric.name |řetězec |dostupnosti |
| dostupnost [0] availabilityMetric. Value |číslo |1,0 nebo 0,0 |
| počet dostupnosti [0] |integer |100/([vzorkovací](./sampling.md) frekvence). Například 4 = &gt; 25%. |
| dostupnost [0] dataSizeMetric.name |řetězec | |
| dostupnost [0] dataSizeMetric. Value |integer | |
| dostupnost [0] durationMetric.name |řetězec | |
| dostupnost [0] durationMetric. Value |číslo |Doba trvání testu. 1E7 = = 1 |
| Zpráva o dostupnosti [0] |řetězec |Diagnostika selhání |
| výsledek dostupnosti [0] |řetězec |Úspěch/neúspěch |
| dostupnost [0] runLocation |řetězec |Geografický zdroj požadavku HTTP REQ |
| dostupnost [0] test |řetězec | |
| dostupnost [0] běhu |řetězec | |
| dostupnost [0] testTimestamp |řetězec | |

## <a name="metrics"></a>Metriky
Vygenerováno pomocí TrackMetric ().

Hodnota metriky se nachází v kontextu. Custom. Metrics [0]

Například:

```json
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
      ]  
    }
  }
}
```

## <a name="about-metric-values"></a>O hodnotách metriky
Hodnoty metriky v sestavách metriky i jinde jsou hlášeny se standardní strukturou objektů. Například:

```json
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
```

V současné době se to může změnit v budoucnu – ve všech hodnotách, které jsou hlášeny z modulů Standard SDK `count==1` a `name` `value` jsou užitečné pouze pole a. Jediný případ, kdy by byl jiný, by byl při psaní vlastních volání TrackMetric, ve kterých jste nastavili další parametry.

Účelem ostatních polí je umožnění agregace metrik v sadě SDK za účelem snížení provozu na portál. Před odesláním každé sestavy metriky můžete například vypočítat průměrně několik následných čtení. Pak byste měli vypočítat minimální, maximální, směrodatnou odchylku a agregovanou hodnotu (součet nebo průměr) a nastavit počet na počet čtení reprezentovaných sestavou.

V tabulkách výše jsme vynechali počet málo používaných polí počet, min, Max, stdDev a sampledValue.

Místo předagregačních metrik můžete použít [vzorkování](./sampling.md) , pokud potřebujete snížit objem telemetrie.

### <a name="durations"></a>Dob trvání
S výjimkou případů, kdy je uvedeno jinak, jsou doby trvání vyjádřeny desáty sekundy, takže 10000000,0 znamená 1 sekundu.

## <a name="see-also"></a>Viz také
* [Application Insights](./app-insights-overview.md)
* [Průběžný export](export-telemetry.md)
* [Ukázky kódu](export-telemetry.md#code-samples)

