---
title: Použití nástrojů Azure IoT pro VSCode k manažerovi zasílání zpráv v CENTRU IT
description: Zjistěte, jak pomocí nástrojů Azure IoT pro kód Visual Studia monitorovat zařízení na cloudové zprávy a odesílat zprávy z cloudu na zařízení v Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 0b081229dcb382786fea03dff358b5cc47d77ee7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912020"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Použití nástrojů Azure IoT pro kód Visual Studia k odesílání a přijímání zpráv mezi zařízením a službou IoT Hub

![Diagram koncového bodu](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) je užitečné rozšíření kódu Visual Studia, které usnadňuje správu ioT hubu a vývoj aplikací IoT. Tento článek se zaměřuje na použití nástrojů Azure IoT pro kód Visual Studia k odesílání a přijímání zpráv mezi zařízením a službou IoT hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Co se naučíte

Dozvíte se, jak pomocí nástrojů Azure IoT pro kód Visual Studia monitorovat zprávy mezi zařízeními a odesílat zprávy z cloudu na zařízení. Zprávy mezi zařízeními a cloudem mohou být data ze senzorů, která vaše zařízení shromažďuje a pak odesílá do vašeho centra IoT Hub. Zprávy z cloudu na zařízení mohou být příkazy, které vaše služba IoT hub odesílá do vašeho zařízení, aby blikala LED dioda připojená k vašemu zařízení.

## <a name="what-you-will-do"></a>Co budete dělat

* Pomocí nástrojů Azure IoT pro kód Visual Studia můžete monitorovat zprávy mezi zařízeními a cloudy.

* Pomocí nástrojů Azure IoT pro kód Visual Studia můžete odesílat zprávy z cloudu na zařízení.

## <a name="what-you-need"></a>Co potřebujete

* Aktivní předplatné Azure.

* Azure IoT hub v rámci vašeho předplatného.

* [Kód visual studia](https://code.visualstudio.com/)

* [Nástroje Azure IoT pro kód VS](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) nebo [otevřete tento odkaz v kódu Visual Studia](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlášení pro přístup k centru IoT hub

1. V **zobrazení Exploreru** v kódu VS rozbalte oddíl **Zařízení Azure IoT Hub** v levém dolním rohu.

2. V místní nabídce klikněte na **Vybrat centrum IoT Hub.**

3. V pravém dolním rohu se zobrazí vyskakovací okno, které vám umožní poprvé se přihlásit do Azure.

4. Po přihlášení se zobrazí váš seznam předplatných Azure a pak vyberte Předplatné Azure a IoT Hub.

5. Seznam zařízení se zobrazí na kartě **Zařízení Služby Azure IoT Hub** během několika sekund.

   > [!Note]
   > Nastavení můžete taky dokončit výběrem možnosti **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu). Zadejte připojovací řetězec zásad **iothubowner** pro službu IoT hub, ke kterému se vaše zařízení IoT připojuje v automaticky otevíranéokno.

## <a name="monitor-device-to-cloud-messages"></a>Sledování zpráv mezi zařízeními a cloudy

Chcete-li sledovat zprávy odeslané ze zařízení do centra IoT hub, postupujte takto:

1. Klikněte pravým tlačítkem myši na zařízení a vyberte **spustit sledování integrovaného koncového bodu událostí**.

2. Monitorované zprávy se zobrazí v zobrazení **OUTPUT** > **Azure IoT Hub.**

3. Chcete-li sledování zastavit, klepněte pravým tlačítkem myši na zobrazení **VÝSTUP** a vyberte příkaz **Zastavit sledování předdefinovaného koncového bodu událostí**.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Pokud chcete do zařízení poslat zprávu z centra IoT, postupujte takto:

1. Klikněte pravým tlačítkem myši na zařízení a vyberte **Odeslat zprávu C2D do zařízení**.

2. Zadejte zprávu do vstupního pole.

3. Výsledky se zobrazí v zobrazení **SLUŽBY OUTPUT** > **Azure IoT Hub.**

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak monitorovat zprávy mezi zařízeními a cloudy a odesílat zprávy z cloudu na zařízení mezi zařízením IoT a službou Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]