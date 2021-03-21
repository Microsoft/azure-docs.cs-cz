---
title: Postup přidání zdroje událostí služby IoT Hub – Azure Time Series Insights | Microsoft Docs
description: Naučte se, jak do prostředí Azure Time Series Insight přidat zdroj událostí služby IoT Hub.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 85773ec66dbe567afcfd1afca1d14aa0119fb8f6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464016"
---
# <a name="add-an-iot-hub-event-source-to-your-azure-time-series-insight-environment"></a>Přidání zdroje událostí služby IoT Hub do prostředí Azure Time Series Insight

Tento článek popisuje, jak pomocí Azure Portal přidat zdroj událostí, který čte data z Azure IoT Hub do vašeho Azure Time Series Insights prostředí.

> [!NOTE]
> Pokyny v tomto článku se vztahují jak na Azure Time Series Insights Gen 1, tak na prostředí Azure Time Series Insight Gen 2.

## <a name="prerequisites"></a>Předpoklady

* Vytvořte [Azure Time Series Insights prostředí](./tutorial-set-up-environment.md).
* Vytvořte [Centrum IoT pomocí Azure Portal](../iot-hub/iot-hub-create-through-portal.md).
* Služba IoT Hub musí mít v nástroji odesílány události aktivní zprávy.
* Vytvořte vyhrazenou skupinu uživatelů ve službě IoT Hub pro prostředí Azure Time Series Insight, které se má využít. Každý zdroj událostí Insight v Azure Time Series musí mít svou vlastní vyhrazenou skupinu uživatelů, která není sdílená s žádným jiným příjemcem. Pokud více čtenářů spotřebovává události ze stejné skupiny příjemců, můžou se všechny čtenáři projevit při selhání. Podrobnosti najdete v příručce pro [vývojáře pro Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidání skupiny příjemců do služby IoT Hub

Aplikace používají skupiny uživatelů k vyžádání dat z Azure IoT Hub. Pokud chcete spolehlivě číst data ze služby IoT Hub, poskytněte vyhrazenou skupinu uživatelů, kterou používá jenom toto prostředí Azure Time Series Insight.

Přidání nové skupiny uživatelů do služby IoT Hub:

1. V [Azure Portal](https://portal.azure.com)Najděte a otevřete Centrum IoT.

1. V části **Nastavení** vyberte **Předdefinované koncové body** a pak vyberte koncový bod **události** .

   [![Na stránce koncové body sestavení vyberte tlačítko události.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. V části **skupiny příjemců** zadejte jedinečný název pro skupinu příjemců. Při vytváření nového zdroje událostí použijte stejný název v prostředí Azure Time Series Insight.

1. Vyberte **Uložit**.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj události

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Všechny prostředky**. Vyberte své prostředí Azure Time Series Insight.

1. V části **Nastavení** vyberte **zdroje událostí** a pak vyberte **Přidat**.

   [![Vyberte zdroje událostí a pak klikněte na tlačítko Přidat.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. V podokně **nový zdroj událostí** pro **název zdroje události** zadejte název, který je jedinečný pro toto prostředí Azure Time Series Insight. Zadejte například **událost-Stream**.

1. Jako **zdroj** vyberte **IoT Hub**.

1. Vyberte hodnotu pro **možnost importu**:

   * Pokud už máte centrum IoT v jednom z vašich předplatných, vyberte **použít IoT Hub z dostupných předplatných**. Tato možnost představuje nejjednodušší přístup.

     [![Vyberte možnosti v podokně nový zdroj událostí.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

   * Následující tabulka popisuje vlastnosti, které jsou požadovány pro možnost **použít IoT Hub z dostupných předplatných** :

       [![Nové podokno zdroje událostí – vlastnosti, které se mají nastavit v možnosti použít IoT Hub z dostupných předplatných](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Vlastnost | Popis |
       | --- | --- |
       | Předplatné | Předplatné, ke kterému patří požadované centrum IoT Hub. |
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

1. Přidejte název vyhrazené skupiny uživatelů služby Azure Time Series Insight, který jste přidali do centra IoT.

1. Vyberte **Vytvořit**.

1. Po vytvoření zdroje událostí Azure Time Series Insight automaticky spustí streamování dat do vašeho prostředí.

## <a name="next-steps"></a>Další kroky

* [Definováním zásad přístupu k datům](./concepts-access-policies.md) Zabezpečte data.

* [Odešle události](time-series-insights-send-events.md) do zdroje událostí.

* Přístup k prostředí v [Průzkumníkovi služby Azure Time Series Insight](https://insights.timeseries.azure.com).
