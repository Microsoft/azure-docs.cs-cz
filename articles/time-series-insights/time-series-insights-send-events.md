---
title: Odesílání událostí do prostředí – Přehledy Azure Time Series | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat centrum událostí, spustit ukázkovou aplikaci a odesílat události do prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254245"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Odesílání událostí do prostředí Time Series Insights pomocí centra událostí

Tento článek vysvětluje, jak vytvořit a nakonfigurovat centrum událostí v Azure Event Hubs. Také popisuje, jak spustit ukázkovou aplikaci pro nabízení událostí do Azure Time Series Insights z centra událostí. Pokud máte existující centrum událostí s událostmi ve formátu JSON, přeskočte tento kurz a zobrazte své prostředí v [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurace centra událostí

1. Chcete-li se dozvědět, jak vytvořit centrum událostí, přečtěte si [dokumentaci centra událostí](https://docs.microsoft.com/azure/event-hubs/).
1. Ve vyhledávacím poli vyhledejte **centra událostí**. Ve vráceném seznamu vyberte **Centra událostí**.
1. Vyberte centrum událostí.
1. Když vytváříte centrum událostí, vytváříte obor názvů centra událostí. Pokud jste ještě nevytvořili centrum událostí v oboru názvů, v nabídce v části **Entity**vytvořte centrum událostí.  

    [![Seznam center událostí](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Po vytvoření centra událostí ho vyberte v seznamu center událostí.
1. V nabídce v části **Entity**vyberte **Event Hubs**.
1. Vyberte název centra událostí, které chcete nakonfigurovat.
1. V části **Přehled**vyberte **skupiny spotřebitelů**a pak vyberte **Skupinu spotřebitelů**.

    [![Vytvoření skupiny spotřebitelů](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Ujistěte se, že jste vytvořili skupinu spotřebitelů, která je používána výhradně zdrojem událostí Time Series Insights.

    > [!IMPORTANT]
    > Ujistěte se, že tuto skupinu spotřebitelů nepoužívá žádná jiná služba, jako je například úloha Azure Stream Analytics nebo jiné prostředí Time Series Insights. Pokud skupinu spotřebitelů používá ostatní služby, operace čtení jsou negativně ovlivněny jak pro toto prostředí, tak pro jiné služby. Pokud používáte **$Default** jako skupinu spotřebitelů, ostatní čtenáři mohou potenciálně znovu použít vaši skupinu spotřebitelů.

1. V nabídce vyberte v části **Nastavení** **položku Zásady sdíleného přístupu**a pak vyberte **Přidat**.

    [![Vyberte zásady sdíleného přístupu a pak vyberte tlačítko Přidat.](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. V podokně **Přidat nové zásady sdíleného přístupu** vytvořte sdílený přístup s názvem **MySendPolicy**. Tato zásada sdíleného přístupu slouží k odesílání událostí v příkladech jazyka C# dále v tomto článku.

    [![Do pole Název zásady zadejte MySendPolicy.](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. V části **Deklarace**zaškrtněte políčko **Odeslat.**

## <a name="add-a-time-series-insights-instance"></a>Přidání instance Time Series Insights

Aktualizace Time Series Insights používá instance k přidání kontextových dat do příchozích telemetrických dat. Data jsou spojena v době dotazu pomocí **ID časové řady**. **ID časové řady** pro ukázkový projekt větrných mlýnů, který používáme později v tomto článku je `id`. Další informace o instancích Time Series Insight a **ID časové řady**najdete v textu [Modely časových řad](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Vytvoření zdroje událostí Přehledy časových řad

1. Pokud jste zdroj událostí nevytvořili, proveďte kroky k [vytvoření zdroje událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Nastavte hodnotu `timeSeriesId`pro . Další informace o **ID časové řady**najdete v textu [Modely časových řad](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Push události na ukázku větrných mlýnů

1. Na panelu hledání vyhledejte **centra událostí**. Ve vráceném seznamu vyberte **Centra událostí**.

1. Vyberte instanci centra událostí.

1. Přejděte na **zásady** > sdíleného**přístupu MySendPolicy**. Zkopírujte hodnotu pro **připojovací řetězec primární klíč**.

    [![Kopírování hodnoty připojovacího řetězce primárního klíče](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Přejděte do části https://tsiclientsample.azurewebsites.net/windFarmGen.html (Soubor > Nový > Jiné). Adresa URL vytváří a spouští simulovaná zařízení větrných mlýnů.
1. Do pole **Připojovací řetězec centra událostí** na webové stránce vložte připojovací řetězec, který jste zkopírovali, do [vstupního pole větrného mlýna](#push-events-to-windmills-sample).
  
    [![Vložení připojovacího řetězce primárního klíče do pole Připojovací řetězec centra událostí](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. **Chcete-li spustit,** vyberte možnost Klepnutí . 

    > [!TIP]
    > Simulátor větrného mlýna také vytváří JSON, který můžete použít jako datovou část s [api dotazu TIME Series Insights GA](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)Query .

    > [!NOTE]
    > Simulátor bude nadále odesílat data, dokud nebude karta prohlížeče zavřená.

1. Vraťte se do centra událostí na webu Azure Portal. Na stránce **Přehled** se zobrazí nové události přijaté centrem událostí.

    [![Stránka Přehled centra událostí, která zobrazuje metriky pro centrum událostí](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Podporované tvary JSON

### <a name="example-one"></a>Příklad jednoho

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

### <a name="example-two"></a>Příklad dva

* **Vstup**: Pole JSON se dvěma objekty JSON. Každý objekt JSON je převeden na událost.

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

* **Vstup**: Objekt JSON s vnořeným polem JSON, který obsahuje dva objekty JSON.

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

* **Výstup**: Dvě události. **Umístění** vlastnosti je zkopírováno do každé události.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Příklad čtyři

* **Vstup**: Objekt JSON s vnořeným polem JSON, který obsahuje dva objekty JSON. Tento vstup ukazuje, že globální vlastnosti mohou být reprezentovány komplexní mstou Objektu JSON.

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
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|tlak|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Další kroky

- [Zobrazení prostředí](https://insights.timeseries.azure.com) v průzkumníku Time Series Insights.

- Další informace o [zprávách zařízení ioT hubu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
