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
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: eaac2e3e2fc489b364729711da151c28cde86d6c
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274700"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Přidání zdroje událostí služby IoT Hub do prostředí Time Series Insights

Tento článek popisuje, jak pomocí Azure Portal přidat zdroj událostí, který čte data z Azure IoT Hub do vašeho Azure Time Series Insights prostředí.

> [!NOTE]
> Pokyny v tomto článku se vztahují na Azure Time Series Insights GA i Time Series Insights prostředí Preview.

## <a name="prerequisites"></a>Požadavky

* Vytvořte [Azure Time Series Insights prostředí](time-series-insights-update-create-environment.md).
* Vytvořte [Centrum IoT pomocí Azure Portal](../iot-hub/iot-hub-create-through-portal.md).
* Služba IoT Hub musí mít v nástroji odesílány události aktivní zprávy.
* Vytvořte vyhrazenou skupinu uživatelů ve službě IoT Hub, která bude Time Series Insights prostředí využívat. Každý Time Series Insights zdroj události musí mít svou vlastní vyhrazenou skupinu uživatelů, která není sdílená s žádným jiným příjemcem. Pokud více čtenářů spotřebovává události ze stejné skupiny příjemců, budou se jim nejspíš zobrazovat chyby. Podrobnosti najdete v příručce pro [vývojáře pro Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidání skupiny příjemců do služby IoT Hub

Aplikace používají skupiny uživatelů k vyžádání dat z Azure IoT Hub. Pokud chcete spolehlivě číst data ze služby IoT Hub, poskytněte vyhrazenou skupinu uživatelů, kterou používá jenom toto Time Series Insights prostředí.

Přidání nové skupiny uživatelů do služby IoT Hub:

1. V [Azure Portal](https://portal.azure.com)Najděte a otevřete Centrum IoT.

1. V části **Nastavení**vyberte **Předdefinované koncové body**a pak vyberte koncový bod **události** .

   [@no__t – 1On na stránce koncové body sestavení vyberte tlačítko události.](media/time-series-insights-how-to-add-an-event-source-iothub/1-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/1-iot-hub.png#lightbox)

1. V části **skupiny příjemců**zadejte jedinečný název pro skupinu příjemců. Při vytváření nového zdroje událostí použijte stejný název v prostředí Time Series Insights.

1. Vyberte **Uložit**.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj události

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **všechny prostředky**. Vyberte prostředí Time Series Insights.

1. V části **topologie prostředí**vyberte **zdroje událostí**a pak vyberte **Přidat**.

   [@no__t 1Select zdroje událostí a potom vyberte tlačítko Přidat.](media/time-series-insights-how-to-add-an-event-source-iothub/2-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/2-add-event-source.png#lightbox)

1. V podokně **nový zdroj událostí** pro **název zdroje události**zadejte název, který je jedinečný pro toto Time Series Insights prostředí. Zadejte například **událost-Stream**.

1. Jako **zdroj**vyberte **IoT Hub**.

1. Vyberte hodnotu pro **možnost importu**:

   * Pokud už máte centrum IoT v jednom z vašich předplatných, vyberte **použít IoT Hub z dostupných předplatných**. Tato možnost představuje nejjednodušší přístup.
   
     [@no__t – možnosti 1Select v novém podokně zdroje událostí](media/time-series-insights-how-to-add-an-event-source-iothub/3-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/3-select-an-import-option.png#lightbox)

    * Následující tabulka popisuje vlastnosti, které jsou požadovány pro možnost **použít IoT Hub z dostupných předplatných** :

       [@no__t – podokno zdroje událostí – vlastnosti, které se mají nastavit v možnosti použít IoT Hub z dostupných předplatných](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png#lightbox)

       | Vlastnost | Popis |
       | --- | --- |
       | Formě | Předplatné, ke kterému patří požadované centrum IoT Hub. |
       | Název centra IoT Hub | Název vybraného centra IoT Hub. |
       | Název zásad centra IoT Hub | Vyberte zásadu sdíleného přístupu. Zásadu sdíleného přístupu najdete na kartě Nastavení centra IoT. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj události *musí* mít oprávnění **služby Connect** . |
       | Klíč zásad centra IoT Hub | Klíč je předem vyplněný. |

    * Pokud je centrum IoT externí pro vaše předplatná, nebo pokud chcete zvolit rozšířené možnosti, vyberte **zadat IoT Hub nastavení ručně**.

      V následující tabulce jsou popsány požadované vlastnosti pro **nastavení zadat IoT Hub ručně**:

       | Vlastnost | Popis |
       | --- | --- |
       | ID předplatného | Předplatné, ke kterému patří požadované centrum IoT Hub. |
       | Skupina prostředků | Název skupiny prostředků, ve které se vytvořilo centrum IoT Hub. |
       | Název centra IoT Hub | Název centra IoT. Při vytváření služby IoT Hub jste zadali název služby IoT Hub. |
       | Název zásad centra IoT Hub | Zásady sdíleného přístupu. Zásadu sdíleného přístupu můžete vytvořit na kartě Nastavení centra IoT. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj události *musí* mít oprávnění **služby Connect** . |
       | Klíč zásad centra IoT Hub | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Azure Service Bus. Sem zadejte primární nebo sekundární klíč. |

    * Obě možnosti sdílejí následující možnosti konfigurace:

       | Vlastnost | Popis |
       | --- | --- |
       | Skupina uživatelů centra IoT Hub | Skupina příjemců, která čte události ze služby IoT Hub. Důrazně doporučujeme, abyste pro zdroj událostí používali vyhrazenou skupinu uživatelů. |
       | Formát serializace události | V současné době je JSON jediným dostupným formátem serializace. Zprávy událostí musí být v tomto formátu nebo žádná data číst nelze. |
       | Název vlastnosti časového razítka | Chcete-li zjistit tuto hodnotu, je třeba porozumět formátu zprávy pro data zprávy, která jsou odeslána do centra IoT. Tato hodnota je **název** konkrétní vlastnosti události v datech zprávy, kterou chcete použít jako časové razítko události. V hodnotě se rozlišují malá a velká písmena. Pokud je ponecháno prázdné, použije se čas zařazení do **fronty události** ve zdroji události jako časové razítko události. |


1. Přidejte do služby IoT Hub název vyhrazené Time Series Insights skupiny uživatelů, kterou jste přidali.

1. Vyberte **vytvořit**.

1. Po vytvoření zdroje událostí Time Series Insights automaticky spouštět streamovaná data do vašeho prostředí.

## <a name="next-steps"></a>Další kroky

* [Definováním zásad přístupu k datům](time-series-insights-data-access.md) Zabezpečte data.

* [Odešle události](time-series-insights-send-events.md) do zdroje událostí.

* Přístup k prostředí v [průzkumníkovi Time Series Insights](https://insights.timeseries.azure.com).
