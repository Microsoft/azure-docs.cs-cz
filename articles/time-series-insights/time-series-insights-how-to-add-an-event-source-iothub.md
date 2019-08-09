---
title: Postup přidání zdroje událostí služby IoT Hub do Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak přidat zdroj událostí, který je připojený ke službě IoT Hub, do vašeho prostředí Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 234fe28cb8bd9dcb97e307836961e2f587e15181
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846636"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Přidání zdroje událostí služby IoT Hub do prostředí Time Series Insights

Tento článek popisuje, jak pomocí Azure Portal přidat zdroj událostí, který čte data z Azure IoT Hub do vašeho Azure Time Series Insights prostředí.

> [!NOTE]
> Pokyny v tomto článku se vztahují na Azure Time Series Insights GA i Time Series Insights prostředí Preview.

## <a name="prerequisites"></a>Požadavky

* Vytvořte [Azure Time Series Insights prostředí](time-series-insights-update-create-environment.md).
* Vytvořte [Centrum IoT pomocí Azure Portal](../iot-hub/iot-hub-create-through-portal.md).
* Služba IoT Hub musí mít v nástroji odesílány události aktivní zprávy.
* Vytvořte vyhrazenou skupinu uživatelů ve službě IoT Hub, která bude Time Series Insights prostředí využívat. Každý zdroj událostí Time Series Insights musí mít svůj vlastní vyhrazenou skupinu spotřebitelů, který není sdílený s další příjemce. Pokud více čtenářům přijímat události z stejnou skupinu uživatelů, jsou pravděpodobně uvidíte selhání všechny nástroje pro čtení. Podrobnosti najdete v příručce pro [vývojáře pro Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidání skupiny příjemců do služby IoT Hub

Aplikace používají skupiny uživatelů k vyžádání dat z Azure IoT Hub. Pokud chcete spolehlivě číst data ze služby IoT Hub, poskytněte vyhrazenou skupinu uživatelů, kterou používá jenom toto Time Series Insights prostředí.

Přidání nové skupiny uživatelů do služby IoT Hub:

1. V Azure Portal Najděte a otevřete Centrum IoT.

1. V části **Nastavení**vyberte **Předdefinované koncové body**a pak vyberte koncový bod **události** .

   [![Na stránce koncové body sestavení vyberte tlačítko události.](media/time-series-insights-how-to-add-an-event-source-iothub/iothub-one.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub-one.png#lightbox)

1. V části **skupiny příjemců**zadejte jedinečný název pro skupinu příjemců. Při vytváření nového zdroje událostí použijte stejný název v prostředí Time Series Insights.

1. Vyberte **Uložit**.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj událostí

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Všechny prostředky**. Vyberte vaše prostředí Time Series Insights.

1. V části **topologie prostředí**vyberte **zdroje událostí**a pak vyberte **přidat**.

   [![Vyberte zdroje událostí a pak klikněte na tlačítko Přidat.](media/time-series-insights-how-to-add-an-event-source-iothub/iothub-two.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub-two.png#lightbox)

1. V podokně **nový zdroj událostí** pro **název zdroje události**zadejte název, který je jedinečný pro toto Time Series Insights prostředí. Zadejte například **událost-Stream**.

1. Jako **zdroj**vyberte **IoT Hub**.

1. Vyberte hodnotu pro **možnost importu**:

   * Pokud už máte centrum IoT v jednom z vašich předplatných, vyberte **použít IoT Hub z dostupných**předplatných. Tato možnost je nejjednodušší přístup.
   
     [![Vyberte možnosti v podokně nový zdroj událostí.](media/time-series-insights-how-to-add-an-event-source-iothub/iothub-three.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub-three.png#lightbox)

    * Následující tabulka popisuje vlastnosti, které jsou požadovány pro možnost **použít IoT Hub z dostupných** předplatných:

       [![Nové podokno zdroje událostí – vlastnosti, které se mají nastavit v možnosti použít IoT Hub z dostupných předplatných](media/time-series-insights-how-to-add-an-event-source-iothub/iothub-four.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub-four.png#lightbox)

       | Vlastnost | Popis |
       | --- | --- |
       | ID předplatného | Vyberte předplatné, ve kterém se služba IoT Hub vytvořila.
       | Název IoT Hubu | Vyberte název centra IoT.
       | Název zásad centra IoT Hub | Vyberte zásady sdíleného přístupu. Zásadu sdíleného přístupu najdete na kartě Nastavení centra IoT. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj události *musí* mít oprávnění **služby Connect** .
       | Klíč zásad centra IoT Hub | Klíč je předem vyplněný.
       | Skupina uživatelů centra IoT Hub | Skupina příjemců, která čte události ze služby IoT Hub. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
       | Formát serializace události | V současné době JSON je k dispozici pouze Serializační formát. Zprávy událostí musí být v tomto formátu nebo žádná data číst nelze. |
       | Název vlastnosti časového razítka | Chcete-li zjistit tuto hodnotu, je třeba porozumět formátu zprávy pro data zprávy, která jsou odeslána do centra IoT. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud je ponecháno prázdné, **čas zařazení události** události zdroj se používá jako časové razítko události. |

    * Pokud je centrum IoT externí pro vaše předplatná, nebo pokud chcete zvolit rozšířené možnosti, vyberte **zadat IoT Hub nastavení ručně**.

      V následující tabulce jsou popsány požadované vlastnosti pro **nastavení zadat IoT Hub ručně**:

       | Vlastnost | Popis |
       | --- | --- |
       | ID předplatného | Předplatné, ve kterém se služba IoT Hub vytvořila.
       | Resource group | Název skupiny prostředků, ve které se vytvořilo centrum IoT Hub.
       | Název IoT Hubu | Název centra IoT. Při vytváření služby IoT Hub jste zadali název služby IoT Hub.
       | Název zásad centra IoT Hub | Zásady sdíleného přístupu. Zásadu sdíleného přístupu můžete vytvořit na kartě Nastavení centra IoT. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj události *musí* mít oprávnění **služby Connect** .
       | Klíč zásad centra IoT Hub | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Azure Service Bus. Zadejte primární a sekundární klíč tady.
       | Skupina uživatelů centra IoT Hub | Skupina příjemců, která čte události ze služby IoT Hub. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
       | Formát serializace události | V současné době JSON je k dispozici pouze Serializační formát. Zprávy událostí musí být v tomto formátu nebo žádná data číst nelze. |
       | Název vlastnosti časového razítka | Chcete-li zjistit tuto hodnotu, je třeba porozumět formátu zprávy pro data zprávy, která jsou odeslána do centra IoT. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud je ponecháno prázdné, **čas zařazení události** události zdroj se používá jako časové razítko události. |

1. Přidejte do služby IoT Hub název vyhrazené Time Series Insights skupiny uživatelů, kterou jste přidali.

1. Vyberte **Vytvořit**.

   [![Tlačítko vytvořit](media/time-series-insights-how-to-add-an-event-source-iothub/iothub-five.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub-five.png#lightbox)

1. Po vytvoření zdroje událostí Time Series Insights automaticky spouštět streamovaná data do vašeho prostředí.

## <a name="next-steps"></a>Další postup

* [Definovat zásady přístupu k datům](time-series-insights-data-access.md) zabezpečit data.

* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.

* Přístup k prostředí na [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com).
