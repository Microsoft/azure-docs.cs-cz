---
title: Použití nástrojů Azure IoT pro VSCode k zasílání zpráv IT centra pro správu
description: Naučte se používat nástroje Azure IoT pro Visual Studio Code k monitorování zpráv ze zařízení do cloudu a posílání cloudu na zprávy zařízení ve službě Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 1c4a840233e576c528e9c58d57eca0b3d524bf4d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566924"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Použití nástrojů Azure IoT pro Visual Studio Code k posílání a přijímání zpráv mezi zařízením a IoT Hub

![Komplexní diagram](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

V tomto článku se dozvíte, jak používat nástroje Azure IoT pro Visual Studio Code k monitorování zpráv ze zařízení do cloudu a k posílání zpráv z cloudu na zařízení. Zprávy ze zařízení do cloudu můžou být senzorem dat, která vaše zařízení shromažďuje a odesílá do služby IoT Hub. Zprávy z cloudu na zařízení můžou být příkazy, které vaše centrum IoT odesílá do vašeho zařízení, aby se mohl rozsvítit indikátor, který je připojený k vašemu zařízení.

[Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) jsou užitečnou příponou Visual Studio Code, která usnadňuje vývoj IoT Hub a správu aplikací IoT. Tento článek se zaměřuje na použití nástrojů Azure IoT pro Visual Studio Code k posílání a přijímání zpráv mezi zařízením a službou IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Požadavky

* Musíte mít aktivní předplatné Azure.

* Azure IoT Hub v rámci vašeho předplatného.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Nástroje Azure IoT pro vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) nebo zkopírujte a vložte tuto adresu URL do okna prohlížeče: `vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se, abyste měli přístup ke službě IoT Hub.

1. V **Průzkumníkovi** zobrazení vs Code rozbalte v levém dolním rohu část **Azure IoT Hub Devices** .

2. V místní nabídce klikněte na **vybrat IoT Hub** .

3. Automaticky otevírané okno se zobrazí v pravém dolním rohu, abyste se mohli poprvé přihlásit do Azure.

4. Po přihlášení se zobrazí seznam předplatných Azure a pak vyberte předplatné Azure a IoT Hub.

5. Seznam zařízení se během několika sekund zobrazí na kartě **zařízení Azure IoT Hub** .

   > [!Note]
   > Nastavení můžete taky dokončit výběrem možnosti **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu). Zadejte připojovací řetězec zásad **iothubowner** pro IoT Hub, ke kterému se vaše zařízení IoT připojuje v místním okně.

## <a name="monitor-device-to-cloud-messages"></a>Monitorování zpráv ze zařízení do cloudu

Pokud chcete monitorovat zprávy odesílané ze zařízení do služby IoT Hub, postupujte takto:

1. Klikněte pravým tlačítkem na své zařízení a vyberte **Spustit sledování integrovaný koncový bod události**.

2. Monitorované zprávy se zobrazí ve **výstupu**  >  zobrazení **IoT Hub v Azure** .

3. Chcete-li ukončit monitorování, klikněte pravým tlačítkem myši na zobrazení **výstupu** a vyberte možnost **Zastavit sledování vestavěného koncového bodu události**.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Pokud chcete poslat zprávu ze služby IoT Hub do svého zařízení, postupujte podle těchto kroků:

1. Klikněte pravým tlačítkem na zařízení a vyberte **Odeslat zprávu C2D do zařízení**.

2. Do vstupního pole zadejte zprávu.

3. Výsledky se zobrazí ve **výstupu**  >  zobrazení **IoT Hub v Azure** .

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak monitorovat zprávy ze zařízení do cloudu a jak odesílat zprávy z cloudu na zařízení mezi zařízením IoT a službou Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]