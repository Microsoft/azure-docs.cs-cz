---
title: Správa zařízení Azure IoT w/ Visual Studio Cloud Explorer
description: Použijte Průzkumníka Cloud pro Visual Studio pro správu zařízení Azure IoT Hub, který obsahuje přímé metody a požadované možnosti správy vlastností twin.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953183"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Použití Průzkumníka Cloud pro správu zařízení Služby Visual Studio pro Azure IoT Hub

![Diagram koncového bodu](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) je užitečné rozšíření Sady Visual Studio, které umožňuje zobrazit prostředky Azure, kontrolovat jejich vlastnosti a provádět klíčové akce vývojáře z visual studia. Dodává se s možnostmi správy, které můžete použít k provádění různých úkolů.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Možnost řízení          | Úkol                    |
|----------------------------|--------------------------------|
| Přímé metody             | Nastavíte zařízení tak, aby spouštění nebo zastavování odesílání zpráv nebo restartování zařízení.                                        |
| Čtení dvojčete zařízení           | Získejte nahlášený stav zařízení. Zařízení například hlásí, že led dioda nyní bliká.                                    |
| Aktualizovat dvojče zařízení         | Přepni zařízení do určitých stavů, například nastavení led diody na zelenou nebo nastavení intervalu odesílání telemetrie na 30 minut.         |
| Zprávy z cloudu na zařízení   | Odesílat oznámení do zařízení. Například, "Je velmi pravděpodobné, že déšť dnes. Nezapomeň si vzít deštník."              |

Podrobnější vysvětlení rozdílů a pokyny k použití těchto možností naleznete v tématu [Pokyny pro komunikaci mezi zařízeními](iot-hub-devguide-d2c-guidance.md) a pokyny ke komunikaci mezi [cloudem](iot-hub-devguide-c2d-guidance.md).

Dvojčata zařízení jsou dokumenty JSON s uloženými informacemi o stavu zařízení, včetně metadat, konfigurací a podmínek. IoT Hub zachová dvojče zařízení pro každé zařízení, které se k němu připojí. Další informace o dvojčatech zařízení najdete [v tématu Začínáme s dvojčaty zařízení](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Co se naučíte

V tomto článku se dozvíte, jak používat Cloud Explorer pro Visual Studio s různými možnostmi správy ve vývojovém počítači.

## <a name="what-you-do"></a>Co děláte

V tomto článku spusťte Průzkumníka Cloud pro Visual Studio s různými možnostmi správy.

## <a name="what-you-need"></a>Co potřebujete

Potřebujete následující předpoklady:

- Aktivní předplatné Azure.

- Azure IoT Hub v rámci vašeho předplatného.

- Aktualizace Microsoft Visual Studio 2017 Aktualizace 9 nebo novější. Tento článek používá [Visual Studio 2017 nebo Visual Studio 2019](https://www.visualstudio.com/vs/).

- Komponenta Cloud Explorer z Instalační služby Visual Studia, která je ve výchozím nastavení vybraná s azure workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Aktualizace Průzkumníka Cloud na nejnovější verzi

Komponenta Průzkumníka cloudu z Instalační služby Visual Studia pro Visual Studio 2017 podporuje jenom monitorování zpráv mezi zařízeními a cloudem. Chcete-li používat Visual Studio 2017, stáhněte a nainstalujte nejnovější [Průzkumníka Cloud .](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Přihlášení pro přístup k rozbočovači

1. V Visual Studiu vyberte **Zobrazit** > **Průzkumníka cloudu** a otevřete Cloud Explorer.

1. Výběrem ikony Správa účtu zobrazíte svá předplatná.

    ![Ikona Správy účtu](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Pokud jste přihlášení k Azure, zobrazí se vaše účty. Pokud se chcete do Azure přihlásit poprvé, zvolte **Přidat účet**.

1. Vyberte předplatná Azure, která chcete použít, a zvolte **Použít**.

1. Rozbalte své předplatné a pak rozbalte **služby IoT Hubs**.  Pod každým rozbočovačem uvidíte svá zařízení pro tento rozbočovač. Kliknutím pravým tlačítkem myši na jedno zařízení zobrazíte přístup k možnostem správy.

    ![Možnosti správy](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Přímé metody

Chcete-li použít přímé metody, postupujte takto:

1. Klepněte pravým tlačítkem myši na zařízení a vyberte **příkaz Vyvolat přímou metodu zařízení**.

1. Zadejte název metody a datovou část do **příkazu Invoke Direct Method**a pak vyberte **OK**.

    Výsledky se zobrazí ve **výstupu**.

## <a name="update-device-twin"></a>Aktualizovat dvojče zařízení

Chcete-li upravit dvojče zařízení, postupujte takto:

1. Klikněte pravým tlačítkem myši na zařízení a vyberte **upravit dvojče zařízení**.

   Otevře se soubor **azure-iot-device-twin.json** s obsahem dvojčete zařízení.

1. Proveďte některé úpravy **značek** nebo **vlastností.požadovaná** pole do souboru **azure-iot-device-twin.json.**

1. Stisknutím **kombinace kláves Ctrl+S** aktualizujte dvojče zařízení.

   Výsledky se zobrazí ve **výstupu**.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Pokud chcete do zařízení poslat zprávu z centra IoT Hub, postupujte takto:

1. Klikněte pravým tlačítkem myši na zařízení a vyberte **Odeslat zprávu C2D**.

1. Zadejte zprávu do **odeslat zprávu C2D** a vyberte **OK**.

   Výsledky se zobrazí ve **výstupu**.

## <a name="next-steps"></a>Další kroky

Naučili jste se používat Cloud Explorer pro Visual Studio s různými možnostmi správy.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
