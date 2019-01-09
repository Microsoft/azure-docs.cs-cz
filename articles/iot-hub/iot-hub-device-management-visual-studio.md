---
title: Azure správy zařízení IoT pomocí Průzkumníka cloudu sady Visual Studio | Dokumentace Microsoftu
description: Pomocí Průzkumníka cloudu sady Visual Studio pro správu zařízení Azure IoT Hub, poskytuje funkci přímých metod a možnosti správy požadované vlastnosti Dvojčete společnosti.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: f0cb740f04970aeed4342511a046862f05aaa736
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54108199"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Pomocí Průzkumníka cloudu sady Visual Studio pro správu zařízení Azure IoT Hub

![Diagram začátku do konce](media/iot-hub-get-started-e2e-diagram/2.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) je užitečná rozšíření sady Visual Studio, která umožňuje zobrazit vaše prostředky Azure, zkoumání jejich vlastností a provádění klíčových vývojářských úkonů ze sady Visual Studio. Obsahuje možnosti správy, které slouží k provádění různých úloh.

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

Zjistíte pomocí Průzkumníka cloudu sady Visual Studio pomocí různých možností správy na svém vývojovém počítači.

## <a name="what-you-do"></a>Co můžete dělat

Spusťte Průzkumníka cloudu sady Visual Studio s různými možnostmi správy.

## <a name="what-you-need"></a>Co potřebujete

- Aktivní předplatné Azure.
- Službu Azure IoT Hub v rámci vašeho předplatného.
- Microsoft Visual Studio 2017 Update 8 nebo novější
- Cloud Explorer komponenty z instalačního programu Visual Studio (zadáno ve výchozím nastavení se sadou Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Průzkumník cloudu aktualizace na nejnovější verzi

Průzkumník cloudu komponenty z instalačního programu sady Visual Studio podporuje pouze monitorování zpráv typu zařízení cloud a cloud zařízení. Je potřeba stáhnout a nainstalovat nejnovější [Průzkumníka cloudu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) pro přístup k možnostem správy.

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se ke službě IoT Hub

1. V sadě Visual Studio **Průzkumníka cloudu** okna, klikněte na ikonu Správa účtů. Můžete otevřít okno Průzkumníka cloudu ze **zobrazení** > **Průzkumníka cloudu** nabídky.

    ![Klikněte na Správa účtů](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Klikněte na tlačítko **spravovat účty** v Průzkumníku cloudu.
1. Klikněte na tlačítko **přidat účet...**  v novém okně pro přihlášení do Azure poprvé.
1. Po přihlášení, zobrazí se seznam vašich předplatných Azure. Vyberte předplatná Azure, kterou chcete zobrazit a klikněte na tlačítko **použít**.
1. Rozbalte **předplatného** > **centra IoT hub** > **váš IoT Hub**, zobrazí se seznam zařízení uzlu služby IoT Hub. Klikněte pravým tlačítkem na jednom zařízení pro přístup k možnostem správy.

    ![Možnosti správy](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>Přímé metody

1. Klikněte pravým tlačítkem na zařízení a vyberte **vyvolání přímé metody zařízení**.
1. Zadejte název metody a datová část vstupního pole.
1. Výsledky se zobrazí v **služby IoT Hub** podokno výstup.

## <a name="read-device-twin"></a>Dvojče zařízení pro čtení

1. Klikněte pravým tlačítkem na zařízení a vyberte **úprava Dvojčete zařízení**.
1. **Azure-iot zařízení – twin.json** otevře se soubor s obsahem dvojče zařízení.

## <a name="update-device-twin"></a>Aktualizovat dvojče zařízení

1. Ujistěte se, některé úpravy **značky** nebo **properties.desired** pole **azure-iot zařízení – twin.json** souboru.
1. Stisknutím klávesy **Ctrl + S** aktualizovat dvojče zařízení.
1. Výsledky se zobrazí v **služby IoT Hub** podokno výstup.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Odeslat zprávu na vaše zařízení ze služby IoT Hub, postupujte podle těchto kroků:

1. Klikněte pravým tlačítkem na zařízení a vyberte **poslat zprávu C2D**.
1. Zadejte zprávu vstupního pole.
1. Výsledky se zobrazí v **služby IoT Hub** podokno výstup.

## <a name="next-steps"></a>Další postup

Jste zjistili, jak pomocí Průzkumníka cloudu sady Visual Studio pomocí různých možností správy.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]