---
title: Správa zařízení Azure IoT w/Visual Studio Cloud Explorer
description: Použijte Průzkumníka cloudu pro Visual Studio pro službu Azure IoT Hub Správa zařízení, která nabízí přímé metody a možnosti správy požadovaných vlastností.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "73953183"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Použití Průzkumníka cloudu pro Visual Studio pro správu zařízení IoT Hub Azure

![Komplexní diagram](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Průzkumník cloudu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) je užitečnou příponou sady Visual Studio, která umožňuje zobrazit prostředky Azure, kontrolovat jejich vlastnosti a provádět klíčové vývojářské akce v rámci sady Visual Studio. Obsahuje možnosti správy, které můžete použít k provádění různých úloh.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Možnost správy          | Úloha                    |
|----------------------------|--------------------------------|
| Přímé metody             | Udělejte zařízení jako takové, jako je spuštění nebo zastavení odesílání zpráv nebo restartování zařízení.                                        |
| Čtení z vlákna zařízení           | Načte stav nahlášeného zařízení. Například zařízení nahlásí, že indikátor LED právě bliká.                                    |
| Aktualizovat dvojitou dvojici zařízení         | Vložte zařízení do určitých stavů, jako je například nastavení indikátoru LED na zelenou nebo nastavení intervalu odesílání telemetrie na 30 minut.         |
| Zprávy z cloudu na zařízení   | Odesílání oznámení do zařízení. Například "je velmi pravděpodobnost, že už ještě nejste v dnešní době deště. Nezapomeňte uvést deštník. "              |

Podrobnější vysvětlení rozdílů a pokynů k používání těchto možností najdete v tématu [pokyny k komunikaci mezi zařízeními a cloudem](iot-hub-devguide-d2c-guidance.md) a [pokyny pro komunikaci z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md).

Dvojčata zařízení jsou dokumenty JSON s uloženými informacemi o stavu zařízení, včetně metadat, konfigurací a podmínek. IoT Hub přetrvává pro každé zařízení, které se k němu připojuje. Další informace o nevlákenách zařízení najdete v tématu [Začínáme s dvojitými zprávami](iot-hub-node-node-twin-getstarted.md)o zařízení.

## <a name="what-you-learn"></a>Co se naučíte

V tomto článku se dozvíte, jak používat Průzkumníka cloudu pro Visual Studio s různými možnostmi správy ve vývojovém počítači.

## <a name="what-you-do"></a>Co dělat

V tomto článku spusťte Průzkumníka cloudu pro Visual Studio s různými možnostmi správy.

## <a name="what-you-need"></a>Co budete potřebovat

Potřebujete následující požadavky:

- Musíte mít aktivní předplatné Azure.

- IoT Hub Azure v rámci vašeho předplatného.

- Microsoft Visual Studio 2017 Update 9 nebo novější. V tomto článku se používá [Visual studio 2017 nebo Visual studio 2019](https://www.visualstudio.com/vs/).

- Součást Průzkumníka cloudu z Instalační program pro Visual Studio, která je standardně vybraná s úlohou Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Aktualizovat Průzkumníka cloudu na nejnovější verzi

Součást Průzkumníka cloudu z Instalační program pro Visual Studio pro Visual Studio 2017 podporuje pouze monitorování zpráv ze zařízení do cloudu a z cloudu na zařízení. Pokud chcete použít Visual Studio 2017, Stáhněte a nainstalujte si nejnovější [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Přihlaste se, abyste měli přístup ke svému centru.

1. V aplikaci Visual Studio vyberte **Zobrazit**  >  **Průzkumníka cloudu** a otevřete Průzkumníka cloudu.

1. Vyberte ikonu Správa účtů a zobrazte si Vaše předplatná.

    ![Ikona správy účtů](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Pokud jste přihlášeni k Azure, zobrazí se vaše účty. Pokud se chcete přihlásit k Azure poprvé, vyberte **Přidat účet**.

1. Vyberte předplatná Azure, která chcete použít, a klikněte na **použít**.

1. Rozbalte své předplatné a potom rozbalte položku **centra IoT**.  V rámci každého centra si můžete zobrazit vaše zařízení pro toto centrum. Kliknutím pravým tlačítkem myši na jedno zařízení získáte přístup k možnostem správy.

    ![Možnosti správy](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Přímé metody

Chcete-li použít přímé metody, proveďte následující kroky:

1. Klikněte pravým tlačítkem na zařízení a vyberte **vyvolat metodu Direct zařízení**.

1. Zadejte název metody a datovou část v **metodě Invoke Direct**a pak vyberte **OK**.

    Výsledky se zobrazí ve **výstupu**.

## <a name="update-device-twin"></a>Aktualizovat dvojitou dvojici zařízení

Chcete-li upravit vlákna zařízení, proveďte následující kroky:

1. Klikněte pravým tlačítkem na zařízení a vyberte **Upravit nevlákenné zařízení**.

   Otevře se **azure-iot-device-twin.jsv** souboru s obsahem vlákna zařízení.

1. Proveďte některé úpravy **značek** nebo **vlastností. požadovaná** pole **azure-iot-device-twin.jsv** souboru.

1. Stisknutím **kombinace kláves CTRL + S** aktualizujte vlákna zařízení.

   Výsledky se zobrazí ve **výstupu**.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Pokud chcete poslat zprávu z IoT Hub do zařízení, postupujte podle těchto kroků:

1. Klikněte pravým tlačítkem na zařízení a vyberte **Odeslat zprávu C2D**.

1. Zadejte zprávu do pole **Odeslat zprávu C2D** a vyberte **OK**.

   Výsledky se zobrazí ve **výstupu**.

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak používat Průzkumníka cloudu pro Visual Studio s různými možnostmi správy.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
