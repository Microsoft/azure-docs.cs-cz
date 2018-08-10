---
title: Odesílání událostí do prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Tento kurz vysvětluje, jak vytvořit a nakonfigurovat Centrum událostí a spustit ukázkovou aplikaci pro odesílání událostí v Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 30b83c54d314934f1de170955eec22e7b2a264b8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629748"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Odesílání událostí do prostředí Time Series Insights pomocí centra událostí
Tento článek vysvětluje, jak vytvořit a nakonfigurovat Centrum událostí a spustit ukázkovou aplikaci pro odesílání událostí. Pokud máte existující centrum událostí s událostmi ve formátu JSON, přeskočte tento kurz a zobrazte své prostředí v [Time Series Insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Konfigurace centra událostí
1. Pokud chcete vytvořit centrum událostí, postupujte podle pokynů uvedených v [dokumentaci](../event-hubs/event-hubs-create.md) ke službě Event Hubs.

2. Vyhledejte **centra událostí** na panelu hledání. Klikněte na tlačítko **Event Hubs** ve vráceném seznamu.

3. Vyberte Centrum událostí kliknutím na jeho název.

4. V části **entity** klikněte v okně Konfigurace střední **Event Hubs** znovu.

5. Vyberte název centra událostí, abyste ho nakonfigurovali.

  ![Výběr skupiny příjemců centra událostí](media/send-events/consumer-group.png)

6. V části **entity**vyberte **skupiny příjemců**.
 
7. Ujistěte se, že vytváříte skupinu příjemců, kterou používá výhradně váš zdroj událostí Time Series Insights.

   > [!IMPORTANT]
   > Zajistěte, aby tuto skupinu příjemců nepoužívala žádná jiná služba (například úloha služby Stream Analytics nebo jiné prostředí Time Series Insights). Pokud skupinu příjemců používají další služby, operace čtení bude negativně ovlivněna pro toto prostředí i ostatní služby. Pokud jako skupinu příjemců používáte $Default, může potenciálně dojít k jejímu opakovanému použití jinými čtenáři.

8. V části **nastavení** záhlaví, vyberte **zásady přístupu pro sdílenou složku**.

9. V Centru událostí vytvořte zásadu **zásady MySendPolicy** , který slouží k odesílání událostí v ukázce csharp.

  ![Vyberte Zásady sdíleného přístupu a klikněte na tlačítko Přidat.](media/send-events/shared-access-policy.png)  

  ![Přidání nové zásady sdíleného přístupu](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Přidání referenční sady dat Time Series Insights 
Vaše telemetrická data pomocí referenčních dat ve službě TSI dává.  Tento kontext přidá význam k vašim datům a usnadňuje filtrování a agregaci.  TSI spojení odkazují na data v době příchozího přenosu dat a nebylo možné připojovat zpětně tato data.  Proto je důležité přidat referenčních dat před přidáním zdroje událostí s daty.  Data, jako jsou umístění nebo senzorů typu jsou užitečné dimenze, které můžete chtít připojit k zařízení/značky/senzoru ID, aby bylo snazší k nařezání a filtr.  

> [!IMPORTANT]
> Máte nakonfigurované referenční sady dat je důležité, pokud nahrajete historická data.

Ujistěte se, že máte referenční data na místě při hromadné nahrání historických dat TSI.  Mějte na paměti, TSI začne okamžitě zjišťovat čtení ze zdroje události připojené k doméně Pokud tento zdroj události má data.  Je vhodné čekat na připojení zdroje událostí do služby TSI nezaevidovali referenční data na místě, zejména v případě, že zdroj události má data v něm. Případně můžete počkat a vložení dat do tohoto zdroje událostí, dokud referenční sada dat je na místě.

Správa referenčních dat, je webové uživatelské rozhraní v Průzkumníku TSI a existuje programový API jazyka C#. Průzkumník TSI má visual uživatele docházet k nahrání souborů nebo vložit v existující referenční datové sady ve formátu JSON nebo CSV. Pomocí rozhraní API můžete vytvářet vlastní aplikace v případě potřeby.

Další informace o správě referenčních dat v Time Series Insights, najdete v článku [článku data](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Vytvoření zdroje událostí Time Series Insights
1. Pokud jste ještě nevytvořili zdroj událostí, postupujte podle [těchto pokynů](time-series-insights-how-to-add-an-event-source-eventhub.md) a vytvořte ho.

2. Zadejte **deviceTimestamp** jako název vlastnosti časového razítka – tato vlastnost se používá jako časové razítko v ukázce C#. V názvu vlastnosti časového razítka se rozlišují malá a velká písmena a při odesílání hodnot do centra událostí ve formátu JSON musí mít formát __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__. Pokud v události tato vlastnost chybí, použije se čas zařazení do fronty centra událostí.

  ![Vytvoření zdroje událostí](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Vzorový kód pro odesílání událostí
1. Přejděte do zásady centra událostí s názvem **zásady MySendPolicy**. Kopírovat **připojovací řetězec** s klíčem zásady.

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

Objekt JSON s vnořeného pole JSON, který obsahuje dva objekty JSON:
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
Všimněte si, že vlastnost "umístění" se kopíruje do obou událostí.

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



## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Zobrazte své prostředí v Průzkumníku Time Series Insights](https://insights.timeseries.azure.com).
