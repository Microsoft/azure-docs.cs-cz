---
title: Odesílání událostí do prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat Centrum událostí a spustit ukázkovou aplikaci pro odesílání událostí, které si můžete prohlédnout v Azure Time Series Insights.
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
ms.custom: seodec18
ms.openlocfilehash: 424476b91537c60a6d7f0f9a854453353bf98633
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557015"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Odesílání událostí do prostředí Time Series Insights pomocí centra událostí

Tento článek vysvětluje, jak vytvořit a nakonfigurovat Centrum událostí ve službě Azure Event Hubs a pak spusťte ukázkovou aplikaci pro odesílání událostí. Pokud máte existující centrum událostí, který má událostí ve formátu JSON, přeskočte tento kurz a zobrazte své prostředí v [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurace centra událostí

1. Zjistěte, jak vytvořit Centrum událostí, najdete v článku [dokumentace ke službě Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. Do vyhledávacího pole vyhledejte **Event Hubs**. Ve vráceném seznamu vyberte **Event Hubs**.
1. Vyberte Centrum událostí.
1. Když vytvoříte Centrum událostí, ve skutečnosti vytváříte obor názvů centra událostí. Pokud jste ještě nevytvořili centra událostí v rámci oboru názvů, v nabídce v části **entity**, vytvoření centra událostí.  

    ![Přehled služby event hubs][1]

1. Po vytvoření centra událostí, vyberte ho v seznamu event hubs.
1. V nabídce v části **entity**vyberte **Event Hubs**.
1. Vyberte název centra událostí, abyste ho nakonfigurovali.
1. V části **entity**vyberte **skupiny příjemců**a pak vyberte **skupinu příjemců**.

    ![Vytvořit skupinu uživatelů][2]

1. Ujistěte se, že vytváříte skupinu příjemců, kterou používá výhradně váš zdroj událostí Time Series Insights.

    > [!IMPORTANT]
    > Zajistěte, aby že tuto skupinu příjemců není používán jinou službu (například úlohy Azure Stream Analytics nebo jiné prostředí Time Series Insights). Pokud skupinu příjemců je použit jinými služby, operace čtení jsou negativně ovlivněna pro toto prostředí i pro jiné služby. Pokud používáte **$Default** jako skupinu příjemců jinými čtenáři může potenciálně opakovaně používat vaše skupina uživatelů.

1. V nabídce v části **nastavení**vyberte **zásady sdíleného přístupu**a pak vyberte **přidat**.

    ![Vyberte zásady sdíleného přístupu a pak vyberte tlačítko Přidat][3]

1. V **přidat nové zásady sdíleného přístupu** podokně vytvoření sdíleného přístupu s názvem **zásady MySendPolicy**. Tato zásada sdíleného přístupu budete používat k odesílání událostí C# příklady dále v tomto článku.

    ![Do pole Název zásady zadejte zásady MySendPolicy][4]

1. V části **deklarace identity**, vyberte **odeslat** zaškrtávací políčko.

## <a name="add-a-time-series-insights-instance"></a>Přidání instance služby Time Series Insights

Aktualizace služby Time Series Insights používá k přidání kontextové údaje do příchozí telemetrická data instance. Data je spojena v době zpracování dotazu **ID řady času**. **ID řady času** windmills ukázkový projekt, který použijeme později v tomto článku je **Id**. Další informace o instancích Time Series Insights a **ID řady času**, naleznete v tématu [čas řady modely](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Vytvoření zdroje událostí Time Series Insights

1. Pokud jste ještě nevytvořili zdroj událostí, dokončete postup [vytvoření zdroje událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Nastavit hodnotu pro `timeSeriesId`. Další informace o **ID řady času**, naleznete v tématu [čas řady modely](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Odesílání událostí (windmills ukázka)

1. Na panelu hledání vyhledejte **Event Hubs**. Ve vráceném seznamu vyberte **Event Hubs**.

1. Vyberte Centrum událostí.

1. Přejděte na **sdílené zásady přístupu** > **RootManageSharedAccessKey**. Zkopírujte hodnotu **připojení stingu – primární klíč**.

    ![Zkopírujte hodnotu primárního klíče připojovací řetězec][5]

1. Přejděte do části https://tsiclientsample.azurewebsites.net/windFarmGen.html (Soubor > Nový > Jiné). Adresa URL spustí windmill simulované zařízení.
1. V **připojovací řetězec centra událostí** pole na webové stránce, vložte připojovací řetězec, který jste zkopírovali v [odesílání událostí](#push-events).
  
    ![Vložte primární klíč připojovacího řetězce v poli připojovací řetězec centra událostí][6]

1. Vyberte **Kliknutím spustíte**. Simulátor generuje instance JSON, které můžete použít přímo.

1. Vraťte se do vašeho centra událostí na webu Azure Portal. Na **přehled** stránky, měli byste vidět nové události přijímá centra událostí:

    ![Stránka Přehled centra událostí, který zobrazuje metriky pro Centrum událostí][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>Podporované tvary JSON

### <a name="sample-1"></a>Ukázka 1

#### <a name="input"></a>Vstup

Jednoduchý objekt JSON:

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output-one-event"></a>Výstup: Jedna událost

|id|časové razítko|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Ukázka 2

#### <a name="input"></a>Vstup

Pole JSON se dvěma objekty JSON. Každý objekt JSON je převedena na událost.

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

#### <a name="output-two-events"></a>Výstup: Dvě události

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

#### <a name="output-two-events"></a>Výstup: Dvě události

Vlastnost **umístění** se kopíruje do každé události.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Ukázka 4

#### <a name="input"></a>Vstup

Objekt JSON s vnořeného pole JSON, který obsahuje dva objekty JSON. Tento vstup ukazuje, že globální vlastnosti může reprezentovat komplexní objekt JSON.

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

#### <a name="output-two-events"></a>Výstup: Dvě události

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zobrazte své prostředí v Průzkumníku Time Series Insights](https://insights.timeseries.azure.com)

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png
