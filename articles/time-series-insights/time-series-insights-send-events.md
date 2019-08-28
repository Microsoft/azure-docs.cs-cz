---
title: Odesílání událostí do prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat Centrum událostí a spustit ukázkovou aplikaci pro odesílání událostí, které si můžete prohlédnout v Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 84eb0e230875b999218b67d47a66a3c92b494253
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072828"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Odesílání událostí do prostředí Time Series Insights pomocí centra událostí

Tento článek vysvětluje, jak vytvořit a nakonfigurovat centrum událostí v Azure Event Hubs. Popisuje také, jak spustit ukázkovou aplikaci pro vložení událostí do Azure Time Series Insights z Event Hubs. Pokud máte existující centrum událostí s událostmi ve formátu JSON, přeskočte tento kurz a podívejte se na vaše prostředí v [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurace centra událostí

1. Zjistěte, jak vytvořit Centrum událostí, najdete v článku [dokumentace ke službě Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. Do vyhledávacího pole vyhledejte **Event Hubs**. Ve vráceném seznamu vyberte **Event Hubs**.
1. Vyberte Centrum událostí.
1. Při vytváření centra událostí vytváříte obor názvů centra událostí. Pokud jste ještě nevytvořili centrum událostí v rámci oboru názvů, vytvořte v nabídce v části **entity**centrum událostí.  

    [![Seznam Center událostí](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. Po vytvoření centra událostí, vyberte ho v seznamu event hubs.
1. V nabídce v části **entity**vyberte možnost **Event Hubs**.
1. Vyberte název centra událostí, abyste ho nakonfigurovali.
1. V části **Přehled**vyberte **skupiny uživatelů**a pak vyberte **Skupina uživatelů**.

    [![Vytvoření skupiny uživatelů](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. Ujistěte se, že jste vytvořili skupinu uživatelů, která je používána výhradně vaším Time Series Insights zdrojem událostí.

    > [!IMPORTANT]
    > Ujistěte se, že tuto skupinu uživatelů nepoužívá žádná jiná služba, například Azure Stream Analytics úloha nebo jiné Time Series Insights prostředí. Pokud skupinu příjemců je použit jinými služby, operace čtení jsou negativně ovlivněna pro toto prostředí i pro jiné služby. Pokud používáte **$Default** jako skupinu příjemců jinými čtenáři může potenciálně opakovaně používat vaše skupina uživatelů.

1. V nabídce v části **Nastavení**vyberte **zásady sdíleného přístupu**a pak vyberte **Přidat**.

    [![Vyberte zásady sdíleného přístupu a pak vyberte tlačítko Přidat.](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. V **přidat nové zásady sdíleného přístupu** podokně vytvoření sdíleného přístupu s názvem **zásady MySendPolicy**. Pomocí této zásady sdíleného přístupu můžete odesílat události v C# příkladech dále v tomto článku.

    [![Do pole název zásady zadejte MySendPolicy](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. V části **deklarace identity**zaškrtněte políčko **Odeslat** .

## <a name="add-a-time-series-insights-instance"></a>Přidání instance služby Time Series Insights

Aktualizace služby Time Series Insights používá k přidání kontextové údaje do příchozí telemetrická data instance. Data je spojena v době zpracování dotazu **ID řady času**. **ID časové řady** ukázkového projektu Windmills, který používáme dále v tomto článku `id`. Další informace o instancích Time Series Insights a **ID řady času**, naleznete v tématu [čas řady modely](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Vytvoření zdroje událostí Time Series Insights

1. Pokud jste ještě nevytvořili zdroj událostí, dokončete postup [vytvoření zdroje událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Nastavit hodnotu pro `timeSeriesId`. Další informace o **ID řady času**, naleznete v tématu [čas řady modely](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Odesílání událostí (windmills ukázka)

1. Na panelu hledání vyhledejte **Event Hubs**. Ve vráceném seznamu vyberte **Event Hubs**.

1. Vyberte instanci centra událostí.

1. Přejděte > na **zásady sdíleného přístupu** **MySendPolicy**. Zkopírujte hodnotu **připojovací řetězec – primární klíč**.

    [![Kopírovat hodnotu pro připojovací řetězec primárního klíče](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. Přejděte do části https://tsiclientsample.azurewebsites.net/windFarmGen.html (Soubor > Nový > Jiné). Adresa URL spustí windmill simulované zařízení.
1. V **připojovací řetězec centra událostí** pole na webové stránce, vložte připojovací řetězec, který jste zkopírovali v [odesílání událostí](#push-events).
  
    [![Vložte připojovací řetězec primárního klíče do pole Připojovací řetězec centra událostí.](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. Vyberte **Kliknutím spustíte**. Simulátor generuje instance JSON, které můžete použít přímo.

1. Vraťte se do vašeho centra událostí na webu Azure Portal. Na stránce **Přehled** se zobrazí nové události, které centrum událostí přijme.

    [![Stránka s přehledem centra událostí, která zobrazuje metriky pro centrum událostí](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>Podporované tvary JSON

### <a name="example-one"></a>Příklad jedné

* **Vstup**: Jednoduchý objekt JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Výstup**: Jedna událost.

    |id|časové razítko|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Příklad 2

* **Vstup**: Pole JSON se dvěma objekty JSON. Každý objekt JSON je převedena na událost.

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

* **Výstup**: Dvě události.

    |id|časové razítko|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Příklad tři

* **Vstup**: Objekt JSON s vnořeného pole JSON, který obsahuje dva objekty JSON.

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

* **Výstup**: Dvě události. Vlastnost **umístění** se kopíruje do každé události.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Příklad 4

* **Vstup**: Objekt JSON s vnořeného pole JSON, který obsahuje dva objekty JSON. Tento vstup ukazuje, že globální vlastnosti může reprezentovat komplexní objekt JSON.

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

* **Výstup**: Dvě události.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Další postup

- [Zobrazte si prostředí](https://insights.timeseries.azure.com) v Průzkumníkovi Time Series Insights.
