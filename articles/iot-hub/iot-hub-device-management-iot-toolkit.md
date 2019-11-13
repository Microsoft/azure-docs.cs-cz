---
title: Správa zařízení Azure IoT pomocí nástrojů Azure IoT pro VSCode
description: Využijte Azure IoT Tools for Visual Studio Code pro správu zařízení Azure IoT Hub, která nabízí přímé metody a možnosti správy požadovaných vlastností.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 03997bb6999af9ebaad36cc95f7834e3b9ed4569
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953194"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Použití nástrojů Azure IoT pro Visual Studio Code pro správu zařízení Azure IoT Hub

![Komplexní diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) jsou užitečnou příponou Visual Studio Code, která usnadňuje vývoj IoT Hub a správu aplikací IoT. Obsahuje možnosti správy, které můžete použít k provádění různých úloh.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Možnost správy          | Úkol                    |
|----------------------------|--------------------------------|
| Přímé metody             | Udělejte zařízení jako takové, jako je spuštění nebo zastavení odesílání zpráv nebo restartování zařízení.                                        |
| Čtení z vlákna zařízení           | Načte stav nahlášeného zařízení. Například zařízení nahlásí, že indikátor LED právě bliká.                                    |
| Aktualizovat dvojitou dvojici zařízení         | Vložte zařízení do určitých stavů, jako je například nastavení indikátoru LED na zelenou nebo nastavení intervalu odesílání telemetrie na 30 minut.         |
| Zprávy typu cloud zařízení   | Odesílání oznámení do zařízení. Například "je velmi pravděpodobnost, že už ještě nejste v dnešní době deště. Nezapomeňte uvést deštník. "              |

Podrobnější vysvětlení rozdílů a pokynů k používání těchto možností najdete v tématu [pokyny k komunikaci mezi zařízeními a cloudem](iot-hub-devguide-d2c-guidance.md) a [pokyny pro komunikaci z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md).

Dvojčata zařízení jsou dokumenty JSON, které obsahují informace o stavu zařízení (metadata, konfigurace a podmínky). IoT Hub přetrvává pro každé zařízení, které se k němu připojuje. Další informace o nevlákenách zařízení najdete v tématu [Začínáme s dvojitými zprávami](iot-hub-node-node-twin-getstarted.md)o zařízení.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Co se naučíte

Naučíte se používat nástroje Azure IoT pro Visual Studio Code s různými možnostmi správy ve vývojovém počítači.

## <a name="what-you-do"></a>Co dělat

Spusťte nástroje Azure IoT Tools pro Visual Studio Code s různými možnostmi správy.

## <a name="what-you-need"></a>Co potřebujete

* Aktivní předplatné Azure.
* Azure IoT Hub v rámci vašeho předplatného.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Nástroje Azure IoT pro vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) nebo [otevřete tento odkaz v Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se ke službě IoT hub

1. V **Průzkumníkovi** zobrazení vs Code rozbalte v levém dolním rohu část **Azure IoT Hub Devices** .

2. V místní nabídce klikněte na **vybrat IoT Hub** .

3. Automaticky otevírané okno se zobrazí v pravém dolním rohu, abyste se mohli poprvé přihlásit do Azure.

4. Po přihlášení se zobrazí seznam předplatných Azure a pak vyberte předplatné Azure a IoT Hub.

5. Seznam zařízení se během několika sekund zobrazí na kartě **zařízení Azure IoT Hub** .

   > [!Note]
   > Nastavení můžete taky dokončit výběrem možnosti **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu). Zadejte připojovací řetězec zásad **iothubowner** pro IoT Hub, ke kterému se vaše zařízení IoT připojuje v místním okně.

## <a name="direct-methods"></a>Přímé metody

1. Klikněte pravým tlačítkem na zařízení a vyberte **vyvolat přímou metodu**. 

2. Do vstupního pole zadejte název metody a datovou část.

3. Výsledky se zobrazí ve **výstupu** > zobrazení sady **nástrojů Azure IoT Hub** .

## <a name="read-device-twin"></a>Čtení z vlákna zařízení

1. Klikněte pravým tlačítkem na zařízení a vyberte **Upravit nevlákenné zařízení**. 

2. Soubor **Azure-IoT-Device-s dvojitou příponou. JSON** se otevře s obsahem vlákna zařízení.

## <a name="update-device-twin"></a>Aktualizovat dvojitou dvojici zařízení

1. Proveďte některé úpravy **značek** nebo **vlastností. požadované** pole.

2. Klikněte pravým tlačítkem na soubor **Azure-IoT-Device-zdvojené. JSON** .

3. Vyberte možnost **aktualizovat dvojitou adresu zařízení** , aby se aktualizovaly vlákna zařízení.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Pokud chcete poslat zprávu ze služby IoT Hub do svého zařízení, postupujte podle těchto kroků:
 
1. Klikněte pravým tlačítkem na zařízení a vyberte **Odeslat zprávu C2D do zařízení**. 

2. Do vstupního pole zadejte zprávu.

3. Výsledky se zobrazí ve **výstupu** > zobrazení sady **nástrojů Azure IoT Hub** .

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak používat rozšíření Azure IoT Tools pro Visual Studio Code s různými možnostmi správy.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
