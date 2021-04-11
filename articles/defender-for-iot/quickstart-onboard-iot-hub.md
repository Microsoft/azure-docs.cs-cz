---
title: 'Rychlý Start: připojení Defenderu pro IoT do řešení založeného na agentovi'
description: V tomto rychlém startu se naučíte, jak připojit a povolit službu Defender for IoT Security ve vašem IoT Hub Azure.
ms.topic: quickstart
ms.date: 1/20/2021
ms.openlocfilehash: 2704f8989ab39825cef052ca5556a2e6461efe75
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384489"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>Rychlý Start: připojení Defenderu pro IoT do řešení založeného na agentovi

Tento článek vysvětluje, jak ve stávající IoT Hub povolit službu Defender for IoT. Pokud v tuto chvíli nemáte IoT Hub, přečtěte si téma [vytvoření IoT Hub pomocí Azure Portal](../iot-hub/iot-hub-create-through-portal.md) pro začátek.

Zabezpečení IoT můžete spravovat prostřednictvím IoT Hub v programu Defender pro IoT. Portál pro správu umístěný v IoT Hub vám umožní provést následující akce: 

- Spravujte zabezpečení IoT Hub.

- Základní Správa zabezpečení zařízení IoT bez instalace agenta na základě telemetrie IoT Hub. 

- Pokročilá Správa zabezpečení zařízení IoT založeného na mikroagentovi.

> [!NOTE]
> Defender pro IoT aktuálně podporuje jenom centra IoT úrovně Standard.

## <a name="prerequisites"></a>Požadavky

Žádné

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>Připojení Defenderu pro IoT k IoT Hub

Pro všechna nová centra IoT je Defender pro IoT ve výchozím nastavení **zapnutý** . V průběhu procesu vytváření IoT Hub můžete ověřit, že je Defender pro IoT přepnut na **zapnuto** .

Chcete-li ověřit, zda je přepínač nastaven na hodnotu **zapnuto**:

1. Přejděte na Azure Portal.

1. V seznamu služeb Azure vyberte **IoT Hub** .

1. Vyberte **Vytvořit**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Na horním panelu nástrojů vyberte tlačítko vytvořit." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Vyberte kartu **Správa** a ověřte, že přepínač **Defender for IoT** je nastaven na **zapnuto**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Zajistěte, aby byl přepínač Defender pro IoT nastavený na zapnuto.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Připojení Defenderu pro IoT k existujícímu IoT Hub

Do existující IoT Hub můžete připojit Defender pro IoT, kde pak můžete monitorovat správu identit zařízení, zařízení do cloudu a Cloud a zařízení.

Připojení Defenderu pro IoT k existujícímu IoT Hub:

1. Přejděte na IoT Hub. 

1. Vyberte IoT Hub k zprovoznění.

1. V části **zabezpečení** vyberte libovolnou možnost.

1. Klikněte na **zabezpečení řešení IoT**   a vyplňte formulář pro registraci. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="Vyberte tlačítko zabezpečení řešení IoT a zabezpečte své řešení.":::

Tlačítko **zabezpečení řešení IoT** se zobrazí, jenom když IoT Hub ještě není připojená, nebo pokud jste při připojování nenechali zapnutý přepínač Defender for **IoT.**

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="Pokud byl váš přepínač nastaven na vypnuto během připojování.":::

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a nakonfigurujte vaše řešení...

> [!div class="nextstepaction"]
> [Vytvoření vlákna programu IoT Micro agent v programu Defender (Preview)](quickstart-create-micro-agent-module-twin.md)
