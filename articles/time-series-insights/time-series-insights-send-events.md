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
ms.date: 12/03/2018
ms.openlocfilehash: c583c2211297acd83f88d23b2b8cbd9f8207927f
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867595"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Odesílání událostí do prostředí Time Series Insights pomocí centra událostí

Tento článek vysvětluje, jak vytvořit a nakonfigurovat Centrum událostí a spustit ukázkovou aplikaci pro odesílání událostí. Pokud máte existující centrum událostí s událostmi ve formátu JSON, přeskočte tento kurz a zobrazte své prostředí v [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurace centra událostí

1. Vytvoření centra událostí, postupujte podle pokynů z centra událostí [dokumentaci](https://docs.microsoft.com/azure/event-hubs/).
1. Vyhledejte `Event Hub` na panelu hledání. Klikněte na tlačítko **Event Hubs** ve vráceném seznamu.
1. Vyberte Centrum událostí kliknutím na jeho název.
1. Při vytváření centra událostí, ve skutečnosti vytváříte Namespace centra událostí.  Pokud máte ještě vytvoření centra událostí v rámci Namespace, vytvořte ji v entity.  

    ![aktualizováno][1]

1. Po vytvoření centra událostí, klikněte na jeho název.
1. V části **entity** klikněte v okně Konfigurace střední **Event Hubs** znovu.
1. Vyberte název centra událostí, abyste ho nakonfigurovali.

    ![Skupina uživatelů][2]

1. V části **entity**vyberte **skupiny příjemců**.
1. Ujistěte se, že vytváříte skupinu příjemců, kterou používá výhradně váš zdroj událostí TSI.

    > [!IMPORTANT]
    > Zajistěte, aby že tuto skupinu příjemců není používán jinou službu (například úloha Stream Analytics nebo jiné prostředí TSI). Pokud skupinu příjemců používají další služby, operace čtení bude negativně ovlivněna pro toto prostředí i ostatní služby. Pokud používáte `$Default` jako skupinu příjemců, by mohlo vést k potenciálním opakované použití jinými čtenáři.

1. V části **nastavení** záhlaví, vyberte **zásady přístupu pro sdílenou složku**.
1. V Centru událostí vytvořte zásadu **zásady MySendPolicy** , který slouží k odesílání událostí v C# vzorku.

    ![sdílený přístup: 1 =][3]

    ![sdílet dvěma přístup][4]

## <a name="add-time-series-insights-instances"></a>Přidání instancí služby Time Series Insights

Aktualizace služby TSI používá k přidání kontextové údaje do příchozí telemetrická data instance. Data se připojí na pomocí dotazu **ID řady času**. **ID řady času** pro windmills ukázkový projekt je `Id`. Další informace o času instancí řady a **čas řady ID**, najdete v tématu [čas řady modely](./time-series-insights-update-tsm.md).

### <a name="create-time-series-insights-event-source"></a>Vytvoření zdroje událostí Time Series Insights

1. Pokud jste ještě nevytvořili zdroj událostí, postupujte podle [těchto pokynů](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub) a vytvořte ho.
1. Zadejte `timeSeriesId` – odkazovat [čas řady modely](./time-series-insights-update-tsm.md) Další informace o **čas řady ID**.

### <a name="push-events-sample-windmills"></a>Odesílání událostí (ukázka windmills)

1. Vyhledání centra událostí v panelu vyhledávání. Klikněte na tlačítko **Event Hubs** ve vráceném seznamu.
1. Vyberte Centrum událostí kliknutím na jeho název.
1. Přejděte na **sdílené zásady přístupu** a potom **RootManageSharedAccessKey**. Kopírovat **připojení stingu – primární klíč**

   ![connection-string][5]

1. Přejděte do části https://tsiclientsample.azurewebsites.net/windFarmGen.html (Soubor > Nový > Jiné). Toto řešení běží windmill simulované zařízení.
1. Vložte připojovací řetězec zkopírovali v kroku 3 **připojovací řetězec centra událostí**.

    ![connection-string][6]

1. Klikněte na **klikněte na tlačítko Start**. Simulátor také vygeneruje Instance JSON, který můžete použít přímo.
1. Vraťte se do vašeho centra událostí. Měli byste vidět nové události se přijaté službou:

   ![telemetrie][7]

<a id="json"></a>

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

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png