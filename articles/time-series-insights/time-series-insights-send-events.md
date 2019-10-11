---
title: Odeslání událostí do prostředí pro Azure Time Series Insights | Microsoft Docs
description: Naučte se konfigurovat centrum událostí a spustit ukázkovou aplikaci, která bude nabízet události, které můžete zobrazit v Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: accf3adea08e713a7a2f06bb175c759ae66a72c0
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274524"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Odeslání událostí do Time Series Insights prostředí pomocí centra událostí

Tento článek vysvětluje, jak vytvořit a nakonfigurovat centrum událostí v Azure Event Hubs. Popisuje také, jak spustit ukázkovou aplikaci pro vložení událostí do Azure Time Series Insights z Event Hubs. Pokud máte existující centrum událostí s událostmi ve formátu JSON, přeskočte tento kurz a podívejte se na vaše prostředí v [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurace centra událostí

1. Informace o tom, jak vytvořit centrum událostí, najdete v [dokumentaci k Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. Do vyhledávacího pole vyhledejte **Event Hubs**. V seznamu vráceno vyberte možnost **Event Hubs**.
1. Vyberte centrum událostí.
1. Při vytváření centra událostí vytváříte obor názvů centra událostí. Pokud jste ještě nevytvořili centrum událostí v rámci oboru názvů, vytvořte v nabídce v části **entity**centrum událostí.  

    [@no__t – 1List z Center událostí](media/send-events/1-event-hub-namespace.png)](media/send-events/1-event-hub-namespace.png#lightbox)

1. Po vytvoření centra událostí ho vyberte v seznamu Center událostí.
1. V nabídce v části **entity**vyberte možnost **Event Hubs**.
1. Vyberte název centra událostí, abyste ho mohli nakonfigurovat.
1. V části **Přehled**vyberte **skupiny uživatelů**a pak vyberte **Skupina uživatelů**.

    [@no__t – 1Create skupina příjemců](media/send-events/2-consumer-group.png)](media/send-events/2-consumer-group.png#lightbox)

1. Ujistěte se, že jste vytvořili skupinu uživatelů, která je používána výhradně vaším Time Series Insights zdrojem událostí.

    > [!IMPORTANT]
    > Ujistěte se, že tuto skupinu uživatelů nepoužívá žádná jiná služba, například Azure Stream Analytics úloha nebo jiné Time Series Insights prostředí. Pokud je skupina uživatelů používána jinými službami, operace čtení jsou negativně ovlivněny pro toto prostředí i pro jiné služby. Pokud jako skupinu příjemců použijete **$Default** , ostatní čtenáři můžou potenciálně znovu použít vaši skupinu uživatelů.

1. V nabídce v části **Nastavení**vyberte **zásady sdíleného přístupu**a pak vyberte **Přidat**.

    [@no__t – zásady sdíleného přístupu 1Select a pak vyberte tlačítko Přidat.](media/send-events/3-shared-access-policy.png)](media/send-events/3-shared-access-policy.png#lightbox)

1. V podokně **Přidat nové zásady sdíleného přístupu** vytvořte sdílený přístup s názvem **MySendPolicy**. Pomocí této zásady sdíleného přístupu můžete odesílat události v C# příkladech dále v tomto článku.

    [@no__t – 1In pole název zásady zadejte MySendPolicy.](media/send-events/4-shared-access-policy-confirm.png)](media/send-events/4-shared-access-policy-confirm.png#lightbox)

1. V části **deklarace identity**zaškrtněte políčko **Odeslat** .

## <a name="add-a-time-series-insights-instance"></a>Přidat instanci Time Series Insights

Time Series Insights Update používá instance pro přidání kontextových dat do příchozích dat telemetrie. Data jsou připojena v době dotazu pomocí **ID časové řady**. **ID časové řady** ukázkového projektu Windmills, který používáme dále v tomto článku, je `id`. Další informace o instancích Insight Series Insights a **ID časových řad**najdete v tématu [modely časových řad](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Vytvoření zdroje událostí Time Series Insights

1. Pokud jste ještě nevytvořili zdroj událostí, proveďte kroky k [Vytvoření zdroje událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Nastavte hodnotu pro `timeSeriesId`. Další informace o **ID časových řad**najdete v tématu [modely časových řad](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Vložení událostí do Windmills Sample

1. Na panelu hledání vyhledejte **Event Hubs**. V seznamu vráceno vyberte možnost **Event Hubs**.

1. Vyberte instanci centra událostí.

1. Přejděte do **zásad sdíleného přístupu** > **MySendPolicy**. Zkopírujte hodnotu **připojovacího řetězce – primární klíč**.

    [@no__t – 1Copy hodnotu pro připojovací řetězec primárního klíče](media/send-events/5-sample-code-connection-string.png)](media/send-events/5-sample-code-connection-string.png#lightbox)

1. Přejít na @no__t – 0. Adresa URL spouští simulovaná zařízení Windmill.
1. Do pole **připojovací řetězec centra událostí** na webové stránce vložte připojovací řetězec, který jste zkopírovali do [vstupního pole Windmill](#push-events-to-windmills-sample).
  
    [@no__t – 1Paste připojovací řetězec primárního klíče v poli připojovací řetězec centra událostí](media/send-events/6-wind-mill-sim.png)](media/send-events/6-wind-mill-sim.png#lightbox)

1. Vyberte **kliknutím spustit**. Simulátor generuje JSON instance, který můžete použít přímo.

1. Vraťte se do centra událostí v Azure Portal. Na stránce **Přehled** se zobrazí nové události, které centrum událostí přijme.

    [@no__t – stránka Přehled centra událostí 1An, která zobrazuje metriky pro centrum událostí](media/send-events/7-telemetry.png)](media/send-events/7-telemetry.png#lightbox)

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
    |zařízení1|2016-01-08T01:08:00Z|

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
    |zařízení1|2016-01-08T01:08:00Z|
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

    |umístění|events.id|události. timestamp|
    |--------|---------------|----------------------|
    |WestUs|zařízení1|2016-01-08T01:08:00Z|
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

    |umístění|manufacturer.name|výrobce. umístění|events.id|události. timestamp|události. data. Type|události. data. Units|události. data. Value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|zařízení1|2016-01-08T01:08:00Z|Citlivost|rozhraní|108,09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|Vibrační|ABS G|217,09|

## <a name="next-steps"></a>Další kroky

- [Zobrazte si prostředí](https://insights.timeseries.azure.com) v Průzkumníkovi Time Series Insights.

- Přečtěte si další informace o [IoT Hubch zprávách zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) .
