---
title: Azure správy zařízení IoT s Azure IoT Tools pro Visual Studio Code | Dokumentace Microsoftu
description: Pomocí nástrojů Azure IoT pro Visual Studio Code pro správu zařízení Azure IoT Hub, poskytuje funkci přímých metod a možnosti správy požadované vlastnosti Dvojčete společnosti.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: ec19045892b975fed7611c7747fa7b2bb4b1515c
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051132"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Použití nástrojů Azure IoT pro Visual Studio Code pro správu zařízení Azure IoT Hub

![Diagram začátku do konce](media/iot-hub-get-started-e2e-diagram/2.png)

[Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) je užitečná rozšíření Visual Studio Code, který usnadňuje služby IoT Hub správu a vývoj aplikací IoT. Obsahuje možnosti správy, které slouží k provádění různých úloh.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Možnost správy          | Úkol                    |
|----------------------------|--------------------------------|
| Přímé metody             | Ujistěte se, zařízení fungují jako je například spouštění nebo zastavování zasílání zpráv nebo restartování zařízení.                                        |
| Dvojče zařízení pro čtení           | Získejte ohlášené stav zařízení. Například zařízení hlásí, že je nyní blikající indikátor LED.                                    |
| Aktualizovat dvojče zařízení         | Umístěte zařízení do některých stavech, například nastavit kontrolku LED na zelenou nebo nastavení intervalu odeslání telemetrie do 30 minut.         |
| Zprávy typu cloud zařízení   | Odesílání oznámení do zařízení. Například "je velmi pravděpodobně déšť ještě dnes. Nezapomeňte uvést zastřešující."              |

Podrobnější vysvětlení na rozdíly a pokyny k použití těchto možností najdete v článku [pokyny komunikace typu zařízení cloud](iot-hub-devguide-d2c-guidance.md) a [pokyny komunikaci typu Cloud zařízení](iot-hub-devguide-c2d-guidance.md).

Dvojčata zařízení jsou dokumenty JSON, které obsahují informace o stavu zařízení (metadata, konfigurace a podmínky). IoT Hub udržuje takové dvojče pro každé zařízení, která se k němu připojuje. Další informace o dvojčata zařízení, najdete v části [Začínáme s dvojčaty zařízení](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Co se naučíte

Zjistíte pomocí nástroje Azure IoT pro Visual Studio Code pomocí různých možností správy na svém vývojovém počítači.

## <a name="what-you-do"></a>Co můžete dělat

Spuštění nástroje pro Azure IoT pro Visual Studio Code pomocí různých možností správy.

## <a name="what-you-need"></a>Co potřebujete

* Aktivní předplatné Azure.
* Azure IoT hub v rámci vašeho předplatného.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Nástroje Azure IoT pro VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se ke službě IoT hub

1. V **Explorer** VS Code, rozbalte **zařízení Azure IoT Hub** části v levém dolním rohu.

2. Klikněte na tlačítko **vyberte centrum IoT** v kontextové nabídce.

3. Automaticky otevírané okno se zobrazí v pravém dolním rohu umožňuje při prvním přihlášení k Azure.

4. Po přihlášení se zobrazí seznam vašich předplatných Azure a potom vyberte předplatné Azure a IoT Hub.

5. Zobrazí se seznam zařízení v **zařízení Azure IoT Hub** kartu za pár sekund.

   > [!Note]
   > Nastavení můžete taky dokončit výběrem možnosti **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu). Zadejte připojovací řetězec služby IoT hub, který připojí zařízení IoT k v místním okně.

## <a name="direct-methods"></a>Přímé metody

1. Klikněte pravým tlačítkem na zařízení a vyberte **vyvolání přímé metody**. 

2. Zadejte název metody a datová část vstupního pole.

3. Výsledky se zobrazí v **výstup** > **Azure IoT Hub Toolkit** zobrazení.

## <a name="read-device-twin"></a>Dvojče zařízení pro čtení

1. Klikněte pravým tlačítkem na zařízení a vyberte **úprava Dvojčete zařízení**. 

2. **Azure-iot zařízení – twin.json** otevře se soubor s obsahem dvojče zařízení.

## <a name="update-device-twin"></a>Aktualizovat dvojče zařízení

1. Ujistěte se, některé úpravy **značky** nebo **properties.desired** pole.

2. Klikněte pravým tlačítkem na **azure-iot zařízení – twin.json** souboru.

3. Vyberte **aktualizovat dvojče zařízení** aktualizovat dvojče zařízení.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Odeslat zprávu na vaše zařízení ze služby IoT hub, postupujte podle těchto kroků:
 
1. Klikněte pravým tlačítkem na zařízení a vyberte **C2D odeslat zprávu do zařízení**. 

2. Zadejte zprávu vstupního pole.

3. Výsledky se zobrazí v **výstup** > **Azure IoT Hub Toolkit** zobrazení.

## <a name="next-steps"></a>Další postup

Jste zjistili, jak používat rozšíření Azure IoT Tools pro Visual Studio Code pomocí různých možností správy.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
