---
title: Odeslání událostí do prostředí – Azure Time Series Insights | Microsoft Docs
description: Naučte se konfigurovat centrum událostí, spouštět ukázkovou aplikaci a posílat do svého Azure Time Series Insightsho prostředí události.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 8ee2cc5473d83eafc2443f3aea7cab6a2e77727c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463557"
---
# <a name="send-events-to-an-azure-time-series-insights-gen1-environment-by-using-an-event-hub"></a>Odeslání událostí do prostředí Azure Time Series Insights Gen1 pomocí centra událostí

> [!CAUTION]
> Toto je Gen1 článek.

Tento článek vysvětluje, jak vytvořit a nakonfigurovat centrum událostí v Azure Event Hubs. Popisuje také, jak spustit ukázkovou aplikaci pro vložení událostí do Azure Time Series Insights z Event Hubs. Pokud máte existující centrum událostí s událostmi ve formátu JSON, přeskočte tento kurz a podívejte se na vaše prostředí v [Azure Time Series Insights](./tutorial-set-up-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurace centra událostí

1. Pokud se chcete dozvědět, jak vytvořit centrum událostí, přečtěte si [dokumentaci Event Hubs](../event-hubs/index.yml).
1. Do vyhledávacího pole vyhledejte **Event Hubs**. V seznamu vráceno vyberte možnost **Event Hubs**.
1. Vyberte centrum událostí.
1. Při vytváření centra událostí vytváříte obor názvů centra událostí. Pokud jste ještě nevytvořili centrum událostí v rámci oboru názvů, vytvořte v nabídce v části **entity** centrum událostí.

    [![Seznam Center událostí](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Po vytvoření centra událostí ho vyberte v seznamu Center událostí.
1. V nabídce v části **entity** vyberte možnost **Event Hubs**.
1. Vyberte název centra událostí, abyste ho mohli nakonfigurovat.
1. V části **Přehled** vyberte **skupiny uživatelů** a pak vyberte **Skupina uživatelů**.

    [![Vytvoření skupiny uživatelů](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Ujistěte se, že jste vytvořili skupinu uživatelů, která je používána výhradně vaším Azure Time Series Insights zdrojem událostí.

    > [!IMPORTANT]
    > Ujistěte se, že tuto skupinu uživatelů nepoužívá žádná jiná služba, například Azure Stream Analytics úloha nebo jiné Azure Time Series Insights prostředí. Pokud je skupina uživatelů používána jinými službami, operace čtení jsou negativně ovlivněny pro toto prostředí i pro jiné služby. Pokud jako skupinu příjemců použijete **$Default** , ostatní čtenáři můžou potenciálně znovu použít vaši skupinu uživatelů.

1. V nabídce v části **Nastavení** vyberte **zásady sdíleného přístupu** a pak vyberte **Přidat**.

    [![Vyberte zásady sdíleného přístupu a pak vyberte tlačítko Přidat.](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. V podokně **Přidat nové zásady sdíleného přístupu** vytvořte sdílený přístup s názvem **MySendPolicy**. Pomocí této zásady sdíleného přístupu můžete odesílat události v příkladech jazyka C# dále v tomto článku.

    [![Do pole název zásady zadejte MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. V části **deklarace identity** zaškrtněte políčko **Odeslat** .

## <a name="add-an-azure-time-series-insights-instance"></a>Přidat instanci Azure Time Series Insights

V Azure Time Series Insights Gen2 můžete přidat kontextová data do příchozí telemetrie pomocí modelu časové řady (TSM). V TSM se vaše značky nebo signály označují jako *instance* a v *polích instance* můžete ukládat kontextová data. Data jsou připojena v době dotazu pomocí **ID časové řady**. **ID časové řady** ukázkového projektu Windmills, který používáme dále v tomto článku `id` . Další informace o ukládání dat v polích instance najdete v tématu Přehled [modelu časové řady](./concepts-model-overview.md) .

### <a name="create-an-azure-time-series-insights-event-source"></a>Vytvoření zdroje události Azure Time Series Insights

1. Pokud jste ještě nevytvořili zdroj událostí, proveďte kroky k [Vytvoření zdroje událostí](./how-to-ingest-data-event-hub.md).

1. Nastavte hodnotu pro `timeSeriesId` . Další informace o **ID časových řad** najdete v tématu [modely časových řad](./concepts-model-overview.md)pro čtení.

### <a name="push-events-to-windmills-sample"></a>Vložení událostí do Windmills Sample

1. Na panelu hledání vyhledejte **Event Hubs**. V seznamu vráceno vyberte možnost **Event Hubs**.

1. Vyberte instanci centra událostí.

1. Přejděte na **zásady sdíleného přístupu**  >  **MySendPolicy**. Zkopírujte hodnotu **připojovacího řetězce – primární klíč**.

    [![Kopírovat hodnotu pro připojovací řetězec primárního klíče](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Přejděte na <https://tsiclientsample.azurewebsites.net/windFarmGen.html>. Adresa URL vytvoří a spustí simulovaná zařízení Windmill.
1. Do pole **připojovací řetězec centra událostí** na webové stránce vložte připojovací řetězec, který jste zkopírovali do [vstupního pole Windmill](#push-events-to-windmills-sample).

    [![Vložte připojovací řetězec primárního klíče do pole Připojovací řetězec centra událostí.](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Vyberte **kliknutím spustit**.

    > [!TIP]
    > Simulátor Windmill také vytvoří JSON, který můžete použít jako datovou část s [rozhraními API pro dotazy GA Azure Time Series Insights GA](/rest/api/time-series-insights/gen1-query).

    > [!NOTE]
    > Simulátor bude pokračovat v posílání dat, dokud nebude zavřena karta prohlížeče.

1. Vraťte se do centra událostí v Azure Portal. Na stránce **Přehled** se zobrazí nové události, které centrum událostí přijalo.

    [![Stránka s přehledem centra událostí, která zobrazuje metriky pro centrum událostí](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Podporované tvary JSON

### <a name="example-one"></a>Příklad jedné

* **Input**: jednoduchý objekt JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Výstup**: jedna událost.

    |id|časové razítko|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Příklad 2

* **Input**: pole JSON se dvěma objekty JSON. Každý objekt JSON je převeden na událost.

    ```JSON
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

* **Výstup**: dvě události.

    |id|časové razítko|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Příklad tři

* **Input**: objekt JSON s vnořeným polem JSON, které obsahuje dva objekty JSON.

    ```JSON
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

* **Výstup**: dvě události. **Umístění** vlastnosti je zkopírováno do každé události.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Příklad 4

* **Input**: objekt JSON s vnořeným polem JSON, které obsahuje dva objekty JSON. Tento vstup ukazuje, že globální vlastnosti mohou být reprezentovány komplexním objektem JSON.

    ```JSON
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

* **Výstup**: dvě události.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|tlak|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Další kroky

* [Zobrazte si prostředí](https://insights.timeseries.azure.com) v Průzkumníkovi Azure Time Series Insights.

* Přečtěte si další informace o [IoT Hubch zprávách zařízení](../iot-hub/iot-hub-devguide-messages-construct.md) .