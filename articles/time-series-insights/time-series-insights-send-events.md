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
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: ae59e8115ca2d1ba69c8a3a099216eb3d98e2658
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237697"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Odesílání událostí do prostředí Time Series Insights pomocí centra událostí

Tento článek vysvětluje, jak vytvořit a nakonfigurovat Centrum událostí ve službě Azure Event Hubs. Také popisuje, jak spustit ukázkovou aplikaci pro odesílání událostí do služby Azure Time Series Insights ze služby Event Hubs. Pokud máte existující centrum událostí s událostmi ve formátu JSON, přeskočte tento kurz a zobrazte své prostředí v [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurace centra událostí

1. Zjistěte, jak vytvořit Centrum událostí, najdete v článku [dokumentace ke službě Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. Do vyhledávacího pole vyhledejte **Event Hubs**. Ve vráceném seznamu vyberte **Event Hubs**.
1. Vyberte Centrum událostí.
1. Když vytvoříte Centrum událostí, kterou vytváříte obor názvů centra událostí. Pokud jste ještě nevytvořili centra událostí v rámci oboru názvů, v nabídce v části **entity**, vytvoření centra událostí.  

    [![Přehled služby event hubs](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. Po vytvoření centra událostí, vyberte ho v seznamu event hubs.
1. V nabídce v části **entity**vyberte **Event Hubs**.
1. Vyberte název centra událostí, abyste ho nakonfigurovali.
1. V části **entity**vyberte **skupiny příjemců**a pak vyberte **skupinu příjemců**.

    [![Vytvořit skupinu uživatelů](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. Ujistěte se, že vytváříte skupinu příjemců, kterou používá výhradně váš zdroj událostí Time Series Insights.

    > [!IMPORTANT]
    > Zajistěte, aby že tuto skupinu příjemců není používán jinou službu, jako je například úlohy Azure Stream Analytics nebo jiné prostředí Time Series Insights. Pokud skupinu příjemců je použit jinými služby, operace čtení jsou negativně ovlivněna pro toto prostředí i pro jiné služby. Pokud používáte **$Default** jako skupinu příjemců jinými čtenáři může potenciálně opakovaně používat vaše skupina uživatelů.

1. V nabídce v části **nastavení**vyberte **zásady sdíleného přístupu**a pak vyberte **přidat**.

    [![Vyberte zásady sdíleného přístupu a pak vyberte tlačítko Přidat](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. V **přidat nové zásady sdíleného přístupu** podokně vytvoření sdíleného přístupu s názvem **zásady MySendPolicy**. Pomocí této zásady sdíleného přístupu k odesílání událostí C# příklady dále v tomto článku.

    [![Do pole Název zásady zadejte zásady MySendPolicy](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. V části **deklarace identity**, vyberte **odeslat** zaškrtávací políčko.

## <a name="add-a-time-series-insights-instance"></a>Přidání instance služby Time Series Insights

Aktualizace služby Time Series Insights používá k přidání kontextové údaje do příchozí telemetrická data instance. Data je spojena v době zpracování dotazu **ID řady času**. **ID řady času** windmills ukázkový projekt, který použijeme později v tomto článku je `id`. Další informace o instancích Time Series Insights a **ID řady času**, naleznete v tématu [čas řady modely](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Vytvoření zdroje událostí Time Series Insights

1. Pokud jste ještě nevytvořili zdroj událostí, dokončete postup [vytvoření zdroje událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Nastavit hodnotu pro `timeSeriesId`. Další informace o **ID řady času**, naleznete v tématu [čas řady modely](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Odesílání událostí (windmills ukázka)

1. Na panelu hledání vyhledejte **Event Hubs**. Ve vráceném seznamu vyberte **Event Hubs**.

1. Vyberte Centrum událostí.

1. Přejděte na **sdílené zásady přístupu** > **RootManageSharedAccessKey**. Zkopírujte hodnotu **připojovací řetězec – primární klíč**.

    [![Zkopírujte hodnotu primárního klíče připojovací řetězec](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. Přejděte do části https://tsiclientsample.azurewebsites.net/windFarmGen.html (Soubor > Nový > Jiné). Adresa URL spustí windmill simulované zařízení.
1. V **připojovací řetězec centra událostí** pole na webové stránce, vložte připojovací řetězec, který jste zkopírovali v [odesílání událostí](#push-events).
  
    [![Vložte primární klíč připojovacího řetězce v poli připojovací řetězec centra událostí](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. Vyberte **Kliknutím spustíte**. Simulátor generuje instance JSON, které můžete použít přímo.

1. Vraťte se do vašeho centra událostí na webu Azure Portal. Na **přehled** stránku, uvidíte nové události, které dostávají v Centru událostí.

    [![Stránka Přehled centra událostí, který zobrazuje metriky pro Centrum událostí](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>Podporované tvary JSON

### <a name="example-one"></a>Příklad jednoho

* **Vstup**: Jednoduchý objekt JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Výstup**: Jednu událost.

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

### <a name="example-three"></a>Příklad tří

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

### <a name="example-four"></a>Příklad čtyř

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

- [Zobrazení prostředí](https://insights.timeseries.azure.com) v Průzkumníku Time Series Insights.
