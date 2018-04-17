---
title: Pro odeslání události do prostředí Azure časové řady Insights | Microsoft Docs
description: Tento kurz vysvětluje, jak vytvořit a nakonfigurovat Centrum událostí a spustíte ukázkovou aplikaci nabízená události zobrazený v Azure časové řady přehledy.
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 04/09/2018
ms.openlocfilehash: c29b90e703a66cbbc25227f9a4307c74d82b03b5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Odesílání událostí do prostředí Time Series Insights pomocí centra událostí
Tento článek vysvětluje, jak vytvořit a nakonfigurovat Centrum událostí a spusťte ukázkové aplikace nabízené události. Pokud máte existující centra událostí s událostmi ve formátu JSON, přeskočte tento kurz a zobrazit prostředí v [časové řady Insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Konfigurace centra událostí
1. Pokud chcete vytvořit centrum událostí, postupujte podle pokynů uvedených v [dokumentaci](../event-hubs/event-hubs-create.md) ke službě Event Hubs.

2. Vyhledejte **centra událostí** v panelu vyhledávání. Klikněte na tlačítko **Event Hubs** ve vráceném seznamu.

3. Vyberte Centrum událostí klepnutím na její název.

4. V části **entity** v okně střední konfigurace klikněte na **Event Hubs** znovu.

5. Vyberte název centra událostí, a nakonfigurovat ho.

  ![Výběr skupiny příjemců centra událostí](media/send-events/consumer-group.png)

6. V části **entity**, vyberte **skupiny příjemců**.
 
7. Ujistěte se, že vytváříte skupinu příjemců, kterou používá výhradně váš zdroj událostí Time Series Insights.

   > [!IMPORTANT]
   > Zajistěte, aby tuto skupinu příjemců nepoužívala žádná jiná služba (například úloha služby Stream Analytics nebo jiné prostředí Time Series Insights). Pokud se skupina uživatelů používají jiné služby, operace čtení negativně ovlivňovat pro toto prostředí a dalších služeb. Pokud jako skupinu příjemců používáte $Default, může potenciálně dojít k jejímu opakovanému použití jinými čtenáři.

8. V části **nastavení** záhlaví, vyberte **zásady přístupu pro sdílenou složku**.

9. V Centru událostí vytvořte **MySendPolicy** sloužící k odesílání událostí v ukázce csharp.

  ![Vyberte Zásady sdíleného přístupu a klikněte na tlačítko Přidat.](media/send-events/shared-access-policy.png)  

  ![Přidání nové zásady sdíleného přístupu](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Přidat časové řady Statistika referenční datové sady 
Použití referenčních dat v TSI contextualizes telemetrická data.  Tento kontext přidá význam ke svým datům a je jednodušší a agregace filtru.  Spojení TSI referenční data v době příjem příchozích dat a se nemůže připojit k zpětně tato data.  Proto je důležité se přidat odkaz na data před přidáním zdroje událostí s daty.  Data jako typ umístění nebo senzor jsou užitečné dimenzí, které chcete připojit k zařízení nebo značky nebo senzoru ID, aby bylo snazší řez a filtru.  

> [!IMPORTANT]
> S nakonfigurované referenční datové sady je velmi důležité při nahrávání historická data.

Ujistěte se, při hromadné odesílání historických dat TSI mít referenční data na místě.  Mějte na paměti, TSI bude okamžitě začít číst od zdroj připojené k události Pokud má tento zdroj událostí data.  Je užitečné má čekat na připojení zdroje událostí TSI dokud máte referenční data na místě, zejména v případě, že zdroj události obsahuje data v ní. Případně můžete počkat nabízet data k danému zdroji událostí, dokud referenční datové sady je na místě.

Ke správě referenční data, není webové uživatelské rozhraní v Průzkumníku TSI a je programový API jazyka C#. Průzkumník TSI má visual uživatelské prostředí pro nahrávání souborů nebo vložení v existující odkaz datové sady jako formátu JSON nebo CSV. Pomocí rozhraní API můžete vytvořit vlastní aplikaci v případě potřeby.

Další informace o správě referenční data v časové řady přehledy, najdete v článku [článku data](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Vytvoření zdroje událostí Time Series Insights
1. Pokud jste ještě nevytvořili zdroj událostí, postupujte podle [těchto pokynů](time-series-insights-how-to-add-an-event-source-eventhub.md) a vytvořte ho.

2. Zadejte **deviceTimestamp** jako název vlastnosti časové razítko – tato vlastnost se používá jako skutečný časové razítko v ukázce C#. V názvu vlastnosti časového razítka se rozlišují malá a velká písmena a při odesílání hodnot do centra událostí ve formátu JSON musí mít formát __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__. Pokud v události tato vlastnost chybí, použije se čas zařazení do fronty centra událostí.

  ![Vytvoření zdroje událostí](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Vzorový kód pro odesílání událostí
1. Přejděte na zásady centra událostí s názvem **MySendPolicy**. Kopírování **připojovací řetězec** klíčem zásad.

  ![Zkopírování připojovacího řetězce zásady MySendPolicy](media/send-events/sample-code-connection-string.png)

2. Spusťte následující kód, který za každé ze tří zařízení odešle 600 událostí. Nahraďte `eventHubConnectionString` vaším připojovacím řetězcem.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>Podporované tvary JSON
### <a name="sample-1"></a>Ukázka 1

#### <a name="input"></a>Vstup

Jednoduchý objekt JSON.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---one-event"></a>Výstup – jedna událost

|id|časové razítko|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Ukázka 2

#### <a name="input"></a>Vstup
Pole JSON se dvěma objekty JSON. Oba objekty JSON se převedou na událost.
```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---two-events"></a>Výstup – dvě události

|id|časové razítko|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Ukázka 3

#### <a name="input"></a>Vstup

Objekt JSON s vnořená pole JSON, který obsahuje dva objekty JSON:
```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---two-events"></a>Výstup – dvě události
Všimněte si, že je vlastnost "umístění" zkopírovali všechny události.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Ukázka 4

#### <a name="input"></a>Vstup

Objekt JSON s vnořeným polem JSON, které obsahuje dva objekty JSON. Tento vstup ukazuje, že globální vlastnosti může reprezentovat komplexní objekt JSON.

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---two-events"></a>Výstup – dvě události

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

### <a name="json-shaping-strategies"></a>Strategie tvarování JSON
Umožňuje použít následující příklad události jako počáteční bod a pak popisují problémy, které souvisí s ho a strategie pro zmírnění těchto problémů.

#### <a name="payload-1"></a>Datová část 1:
```json
[{
            "messageId": "LINE_DATA",
            "deviceId": "FXXX",
            "timestamp": 1522355650620,
            "series": [{
                        "chId": 3,
                        "value": -3750.0
                  }, {
                        "chId": 13,
                        "value": 0.58015072345733643
                  }, {
                        "chId": 11,
                        "value": 800.0
                  }, {
                        "chId": 21,
                        "value": 0.0
                  }, {
                        "chId": 14,
                        "value": -999.0
                  }, {
                        "chId": 37,
                        "value": 2.445906400680542
                  }, {
                        "chId": 39,
                        "value": 0.0
                  }, {
                        "chId": 40,
                        "value": 1.0
                  }, {
                        "chId": 1,
                        "value": 1.0172575712203979
                  }
            ],
            "EventProcessedUtcTime": "2018-03-29T20:36:21.3245900Z",
            "PartitionId": 2,
            "EventEnqueuedUtcTime": "2018-03-29T20:34:11.0830000Z",
            "IoTHub": {
                  "MessageId": "<17xxx2xx-36x0-4875-9x1x-x428x41x1x68>",
                  "CorrelationId": "<x253x5xx-7xxx-4xx3-91x4-xxx3bx2xx0x3>",
                  "ConnectionDeviceId": "AAAA-ZZ-001",
                  "ConnectionDeviceGenerationId": "<123456789012345678>",
                  "EnqueuedTime": "2018-03-29T20:34:10.7990000Z",
                  "StreamId": null
            }
      }
]
 ```

Pokud toto pole událostí jako datové části nabízené TSI, bude uložen jako jedna událost pro každou hodnotu míry. Díky tomu můžete vytvořit nad události, které nemusí být právě ideální. Všimněte si, že můžete použít referenční data v TSI přidat smysluplné názvy jako vlastnosti.  Například můžete vytvořit odkaz na datovou sadu s vlastností klíč = chId:  

chId měr jednotky 24 modul těžba ropy přetížení PSI 25 CALC čerpadla míra bbl za minutu

Další informace o správě referenční data v časové řady přehledy, najdete v článku [článku data](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-add-reference-data-set).

Dalším problémem s první datové části je, že časové razítko je uvedena v milisekundách. TSI přijímá pouze ve formátu ISO časová razítka. Jedno řešení je ponechte výchozí chování časové razítko v TSI, který má používat časové razítko zařazených do fronty.

Jako alternativu k datové části výše Podíváme se na další příklad.  

#### <a name="payload-2"></a>Datová část 2:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "STATE Engine State": 1,
      "unit": "NONE"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "MPC_AAAA-ZZ-001",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Well Head Px 1": -494162.8515625,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "unit": "bbl/min"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Fuel Pressure": 0,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Oil Pressure": 0.58015072345733643,
      "unit": "psi"
}
```

Jako datovou část 1 uloží TSI každou každých měřená hodnotu jako jedinečný událost.  Významné rozdílem je, že budou číst TSI *časové razítko* jako správně sem jako ISO.  

Pokud potřebujete omezit počet událostí odeslaných, může odeslat informace jako následující.  

#### <a name="payload-3"></a>Datová část 3:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```
Zde je jeden konečný návrh.

#### <a name="payload-4"></a>Datová část 4:
```json
{
              "line": "Line01",
              "station": "Station 11",
              "gatewayid": "AAAA-ZZ-001",
              "deviceid": "F12XX",
              "timestamp": "2018-03-29T20:34:15.0000000Z",
              "CALC Pump Rate": {
                           "value": 0,
                           "unit": "bbl/min"
              },
              "Engine Oil Pressure": {
                           "value": 0.58015072345733643,
                           "unit": "psi"
              },
              "Engine Fuel Pressure": {
                           "value": 0,
                           "unit": "psi"
              }
}
```

Tento příklad ukazuje výstup po shrnutí JSON:

```json
{
      "line": "Line01",
      "station": "Station 11",,
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate.value": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure.value": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure.value": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```

Máte volného definovat různé vlastnosti pro každou kanálů uvnitř vlastní objekt json přitom počtu událostí nízkou. Tento přístup plochou zabírají více místa, což je důležité vzít v úvahu. Kapacita TSI je založen na události a velikost, nastane dříve.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Zobrazit v Průzkumníku časové řady Statistika prostředí](https://insights.timeseries.azure.com).
