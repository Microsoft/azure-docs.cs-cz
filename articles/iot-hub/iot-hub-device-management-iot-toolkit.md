---
title: Správa zařízení Azure IoT pomocí nástrojů Azure IoT pro VSCode
description: Použijte nástroje Azure IoT Tools for Visual Studio Code for Azure IoT Hub správa zařízení, představovat přímé metody a twin požadované možnosti správy vlastností.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: d85e0e967dd802a77ccbc11b884d7a9f2891524d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688095"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Použití nástrojů Azure IoT tools for Visual Studio Code for Azure IoT Hub management

![Diagram koncového bodu](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) je užitečné rozšíření kódu Visual Studia, které usnadňuje správu ioT hubu a vývoj aplikací IoT. Dodává se s možnostmi správy, které můžete použít k provádění různých úkolů.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Možnost řízení          | Úkol                    |
|----------------------------|--------------------------------|
| Přímé metody             | Nastavíte zařízení tak, aby spouštění nebo zastavování odesílání zpráv nebo restartování zařízení.                                        |
| Čtení dvojčete zařízení           | Získejte nahlášený stav zařízení. Zařízení například hlásí, že led dioda nyní bliká.                                    |
| Aktualizovat dvojče zařízení         | Přepni zařízení do určitých stavů, například nastavení led diody na zelenou nebo nastavení intervalu odesílání telemetrie na 30 minut.         |
| Zprávy z cloudu na zařízení   | Odesílat oznámení do zařízení. Například, "Je velmi pravděpodobné, že déšť dnes. Nezapomeň si vzít deštník."              |

Podrobnější vysvětlení rozdílů a pokyny k použití těchto možností naleznete v tématu [Pokyny pro komunikaci mezi zařízeními](iot-hub-devguide-d2c-guidance.md) a pokyny ke komunikaci mezi [cloudem](iot-hub-devguide-c2d-guidance.md).

Dvojčata zařízení jsou dokumenty JSON, které obsahují informace o stavu zařízení (metadata, konfigurace a podmínky). IoT Hub zachová dvojče zařízení pro každé zařízení, které se k němu připojí. Další informace o dvojčatech zařízení najdete [v tématu Začínáme s dvojčaty zařízení](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Co se naučíte

Naučíte se pomocí Nástroje Azure IoT pro Visual Studio kód s různými možnostmi správy na vývojovém počítači.

## <a name="what-you-do"></a>Co děláte

Spouštějte nástroje Azure IoT pro kód Visual Studia s různými možnostmi správy.

## <a name="what-you-need"></a>Co potřebujete

* Aktivní předplatné Azure.
* Azure IoT hub v rámci vašeho předplatného.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Nástroje Azure IoT pro kód VS](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) nebo zkopírujte tuto`vscode:extension/vsciot-vscode.azure-iot-tools`adresu URL a vložte ji do okna prohlížeče: .

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlášení pro přístup k centru IoT hub

1. V **zobrazení Exploreru** v kódu VS rozbalte oddíl **Zařízení Azure IoT Hub** v levém dolním rohu.

2. V místní nabídce klikněte na **Vybrat centrum IoT Hub.**

3. V pravém dolním rohu se zobrazí vyskakovací okno, které vám umožní poprvé se přihlásit do Azure.

4. Po přihlášení se zobrazí váš seznam předplatných Azure a pak vyberte Předplatné Azure a IoT Hub.

5. Seznam zařízení se zobrazí na kartě **Zařízení Služby Azure IoT Hub** během několika sekund.

   > [!Note]
   > Nastavení můžete taky dokončit výběrem možnosti **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu). Zadejte připojovací řetězec zásad **iothubowner** pro službu IoT hub, ke kterému se vaše zařízení IoT připojuje v automaticky otevíranéokno.

## <a name="direct-methods"></a>Přímé metody

1. Klepněte pravým tlačítkem myši na zařízení a vyberte **příkaz Vyvolat přímou metodu**. 

2. Zadejte název metody a datovou část do vstupního pole.

3. Výsledky se zobrazí v zobrazení **SLUŽBY OUTPUT** > **Azure IoT Hub.**

## <a name="read-device-twin"></a>Čtení dvojčete zařízení

1. Klikněte pravým tlačítkem myši na zařízení a vyberte **upravit dvojče zařízení**. 

2. Soubor **azure-iot-device-twin.json** se otevře s obsahem dvojčete zařízení.

## <a name="update-device-twin"></a>Aktualizovat dvojče zařízení

1. Proveďte některé úpravy **značek** nebo **polí properties.desired.**

2. Klikněte pravým tlačítkem myši na soubor **azure-iot-device-twin.json.**

3. Chcete-li aktualizovat dvojče zařízení, vyberte **aktualizovat dvojče zařízení.**

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Pokud chcete do zařízení poslat zprávu z centra IoT, postupujte takto:
 
1. Klikněte pravým tlačítkem myši na zařízení a vyberte **Odeslat zprávu C2D do zařízení**. 

2. Zadejte zprávu do vstupního pole.

3. Výsledky se zobrazí v zobrazení **SLUŽBY OUTPUT** > **Azure IoT Hub.**

## <a name="next-steps"></a>Další kroky

Naučili jste se používat rozšíření nástrojů Azure IoT pro kód Sady Visual Studio s různými možnostmi správy.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
