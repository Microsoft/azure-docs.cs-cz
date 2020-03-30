---
title: Jak přidat zdroj událostí centra IoT – Přehledy Azure Time Series | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přidat zdroj událostí centra IoT do prostředí Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/30/2020
ms.custom: seodec18
ms.openlocfilehash: 3ea73e2ca20faea30294bc5d5e1788415095c39f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905362"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Přidání zdroje událostí centra IoT hub do prostředí Time Series Insights

Tento článek popisuje, jak pomocí portálu Azure přidat zdroj událostí, který čte data z Azure IoT Hub do prostředí Azure Time Series Insights.

> [!NOTE]
> Pokyny v tomto článku platí jak pro Azure Time Series Insights GA a Time Series Insights Preview prostředí.

## <a name="prerequisites"></a>Požadavky

* Vytvořte [prostředí Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Vytvořte [centrum IoT pomocí portálu Azure](../iot-hub/iot-hub-create-through-portal.md).
* V centru IoT musí být odesílány aktivní události zpráv.
* Vytvořte vyhrazenou skupinu spotřebitelů v centru IoT, ze které můžete využívat prostředí Time Series Insights. Každý zdroj událostí Time Series Insights musí mít vlastní vyhrazenou skupinu spotřebitelů, která není sdílena s žádným jiným spotřebitelem. Pokud více čtenářů spotřebovávají události ze stejné skupiny spotřebitelů, všechny čtenáři pravděpodobně vykazují chyby. Podrobnosti načtěte [v průvodci vývojáři centra Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidání skupiny spotřebitelů do centra IoT hub

Aplikace používají skupiny spotřebitelů k vyprávění dat z Azure IoT Hub. Chcete-li spolehlivě číst data z centra IoT, zadejte vyhrazenou skupinu spotřebitelů, která se používá pouze v tomto prostředí Time Series Insights.

Přidání nové skupiny spotřebitelů do centra IoT:

1. Na [webu Azure Portal](https://portal.azure.com)najděte a otevřete své centrum IoT.

1. V části **Nastavení**vyberte **předdefinované koncové body**a pak vyberte koncový bod **Události.**

   [![Na stránce Vestavěné koncové body vyberte tlačítko Události.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. V části **Skupiny příjemce**zadejte jedinečný název skupiny spotřebitelů. Stejný název použijte v prostředí Time Series Insights při vytváření nového zdroje událostí.

1. Vyberte **Uložit**.

## <a name="add-a-new-event-source"></a>Přidání nového zdroje událostí

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V nabídce vlevo vyberte **Všechny prostředky**. Vyberte vaše prostředí Time Series Insights.

1. V části **Nastavení**vyberte **Zdroje událostí**a pak vyberte **Přidat**.

   [![Vyberte Zdroje událostí a pak vyberte tlačítko Přidat.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. V podokně **Nový zdroj událostí** zadejte do pole Název zdroje **události**název, který je jedinečný pro toto prostředí Přehledy časových řad. Zadejte například **event-stream**.

1. V **okně Zdroj**vyberte **Položku IoT Hub**.

1. Vyberte hodnotu pro **volbu Import**:

   * Pokud už máte službu IoT hub v jednom z vašich předplatných, vyberte **Použít službu IoT Hub z dostupných předplatných**. Tato možnost je nejjednodušší přístup.
   
     [![Výběr možností v podokně Nový zdroj událostí](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * Následující tabulka popisuje vlastnosti, které jsou požadovány pro **možnost Použít službu IoT Hub z dostupných předplatných:**

       [![Podokno Nový zdroj událostí – vlastnosti, které se mají nastavit v centru Use IoT Hub z dostupných předplatných](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Vlastnost | Popis |
       | --- | --- |
       | Předplatné | Předplatné, do které patří požadovaný iot hub. |
       | Název centra IoT | Název vybraného centra iot. |
       | Název zásad centra IoT hub | Vyberte zásady sdíleného přístupu. Zásady sdíleného přístupu najdete na kartě Nastavení služby IoT hub. Každá zásada sdíleného přístupu má název, nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro zdroj událostí *musí* mít oprávnění **pro připojení služby.** |
       | Klíč zásad centra IoT | Klíč je předem vyplněný. |

    * Pokud je centrum IoT externí pro vaše předplatná nebo pokud chcete zvolit pokročilé možnosti, vyberte **ručně poskytněte nastavení služby IoT Hub**.

      Následující tabulka popisuje požadované vlastnosti **pro nastavení služby IoT Hub ručně**:

       | Vlastnost | Popis |
       | --- | --- |
       | ID předplatného | Předplatné, do které patří požadovaný iot hub. |
       | Skupina prostředků | Název skupiny prostředků, ve kterém byla vytvořena centrum IoT. |
       | Název centra IoT | Název vašeho centra IoT. Při vytváření centra IoT jste zadali název centra IoT hub. |
       | Název zásad centra IoT hub | Zásady sdíleného přístupu. Zásady sdíleného přístupu můžete vytvořit na kartě Nastavení služby IoT hub. Každá zásada sdíleného přístupu má název, nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro zdroj událostí *musí* mít oprávnění **pro připojení služby.** |
       | Klíč zásad centra IoT | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Azure Service Bus. Zde zadejte primární nebo sekundární klíč. |

    * Obě možnosti sdílejí následující možnosti konfigurace:

       | Vlastnost | Popis |
       | --- | --- |
       | Skupina spotřebitelů centra IoT hub | Skupina spotřebitelů, která čte události z centra IoT. Důrazně doporučujeme použít vyhrazenou skupinu spotřebitelů pro zdroj událostí. |
       | Formát serializace události | V současné době json je pouze k dispozici serializace formát. Zprávy o událostech musí být v tomto formátu nebo nelze číst žádná data. |
       | Název vlastnosti časového razítka | Chcete-li zjistit tuto hodnotu, musíte pochopit formát zprávy dat zpráv, která je odeslána do služby IoT hub. Tato hodnota je **název** vlastnosti konkrétní události v datech zprávy, která chcete použít jako časové razítko události. Hodnota rozlišuje malá a velká písmena. Pokud je ponecháno prázdné, **čas zařazení události do fronty** ve zdroji událostí se používá jako časové razítko události. |


1. Přidejte název skupiny zákazníků Time Series Insights, který jste přidali do služby IoT hub.

1. Vyberte **Vytvořit**.

1. Po vytvoření zdroje událostí Time Series Insights automaticky spustí streamování dat do vašeho prostředí.

## <a name="next-steps"></a>Další kroky

* [Definujte zásady přístupu k datům](time-series-insights-data-access.md) pro zabezpečení dat.

* [Odešlete události](time-series-insights-send-events.md) do zdroje událostí.

* Přístup k vašemu prostředí v [průzkumníku Time Series Insights](https://insights.timeseries.azure.com).
