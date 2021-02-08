---
title: Připojování k programu Defender pro řešení založené na agentech IoT
description: Naučte se integrovat a povolit službu Defender for IoT Security ve vašem Azure IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809121"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>Připojování k programu Defender pro řešení založené na agentech IoT

Tento článek vysvětluje, jak ve stávající IoT Hub povolit službu Defender for IoT. Pokud v tuto chvíli nemáte IoT Hub, přečtěte si téma [vytvoření IoT Hub pomocí Azure Portal](../iot-hub/iot-hub-create-through-portal.md) pro začátek.

Zabezpečení IoT můžete spravovat prostřednictvím IoT Hub v programu Defender pro IoT. Portál pro správu umístěný v IoT Hub vám umožní provést následující akce: 

- Spravujte zabezpečení IoT Hub.

- Základní Správa zabezpečení zařízení IoT bez instalace agenta na základě telemetrie IoT Hub. 

- Pokročilá Správa zabezpečení zařízení IoT založeného na mikroagentovi.

> [!NOTE]
> Defender pro IoT aktuálně podporuje jenom centra IoT úrovně Standard.

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>Připojení k programu Defender pro IoT v IoT Hub

Pro všechna nová centra IoT je Defender pro IoT ve výchozím nastavení **zapnutý** . V průběhu procesu vytváření IoT Hub můžete ověřit, že je Defender pro IoT přepnut na **zapnuto** .

Chcete-li ověřit, zda je přepínač nastaven na hodnotu **zapnuto**:

1. Přejděte na Azure Portal.

1. V seznamu služeb Azure vyberte **IoT Hub** .

1. Vyberte **Vytvořit**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Na horním panelu nástrojů vyberte tlačítko vytvořit." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Vyberte kartu **Správa** a ověřte, že přepínač **Defender for IoT** je nastaven na **zapnuto**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Zajistěte, aby byl přepínač Defender pro IoT nastavený na zapnuto.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Připojení Defenderu pro IoT k existujícímu IoT Hub

Můžete monitorovat způsoby komunikace zařízení, zařízení do cloudu a cloudu a zařízení a spustit službu: 

1. Přejděte na IoT Hub. 

1. Vyberte nabídku **Přehled zabezpečení**   . 

1. Klikněte na zabezpečení řešení IoT a vyplňte formulář pro registraci. 


## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a nakonfigurujte vaše řešení...

> [!div class="nextstepaction"]
> [Vytvoření vlákna programu IoT Micro agent v programu Defender (Preview)](quickstart-create-micro-agent-module-twin.md)
