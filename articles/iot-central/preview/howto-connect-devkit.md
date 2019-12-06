---
title: Připojení zařízení DevKit k aplikaci Azure IoT Central | Microsoft Docs
description: Jako vývojář zařízení se naučíte připojit zařízení IoT DevKit MXChip k aplikaci Azure IoT Central pomocí technologie Plug and Play IoT.
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 32dd3fa1fc137d786174e47d842f762c2a479d64
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848936"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application-preview-features"></a>Připojení zařízení MXChip IoT DevKit k aplikaci Azure IoT Central (funkce ve verzi Preview)

V tomto článku se dozvíte, jak připojit zařízení MXChip IoT DevKit (DevKit) k aplikaci Azure IoT Central. Zařízení používá pro zařízení DevKit certifikovaný model IoT technologie Plug and Play ke konfiguraci připojení k IoT Central.

V tomto článku s postupem:

- Získejte podrobnosti o připojení z vaší aplikace IoT Central.
- Připravte zařízení a připojte ho k aplikaci IoT Central.
- Zobrazení telemetrie a vlastností ze zařízení v IoT Central.

## <a name="prerequisites"></a>Předpoklady

K dokončení kroků v tomto článku budete potřebovat následující zdroje:

- [Zařízení DevKit](https://aka.ms/iot-devkit-purchase).
- Aplikace IoT Central vytvořená z šablony **aplikace ve verzi Preview** . Můžete postupovat podle kroků v části [Vytvoření aplikace IoT technologie Plug and Play](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Získat podrobnosti o připojení zařízení

1. V aplikaci Azure IoT Central vyberte kartu **šablony zařízení** a vyberte **Nová**. V části **použití předem nakonfigurované šablony zařízení**vyberte ze seznamu možnost **MXChip IoT DevKit** . A klikněte na tlačítko **Další: přizpůsobit** a **vytvořit**.

    ![Šablona zařízení pro MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Vyberte kartu **zařízení** , v seznamu zařízení vyberte **MXChip IoT DevKit** a vyberte **Nový** , chcete-li vytvořit nové zařízení ze šablony zařízení.

    ![Nové zařízení](media/howto-connect-devkit/new-device.png)

1. V automaticky otevíraném okně zadejte **ID zařízení** jako `SampleDevKit` a **název zařízení** jako `MXChip IoT DevKit - Sample`. Zajistěte, aby se **simulovaná** možnost vypnula. Potom vyberte **Vytvořit**.

    ![ID a název zařízení](media/howto-connect-devkit/device-id-name.png)

1. Klikněte na zařízení, které jste právě vytvořili, a vyberte **připojit**. Poznamenejte si **Rozsah ID**, **ID zařízení** a **primární klíč**.

    ![Informace o připojení zařízení](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Příprava zařízení

1. Stáhněte si nejnovější [předem sestavený firmware Azure IoT Central technologie Plug and Play](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) pro zařízení DevKit z GitHubu.

1. Připojte zařízení DevKit k vývojovému počítači pomocí kabelu USB. V systému Windows se otevře okno Průzkumník souborů na jednotce namapované na úložiště na zařízení DevKit. Například jednotka může být volána **AZ3166 (D:)** .

1. Přetáhněte soubor **iotc_devkit. bin** do okna jednotky. Po dokončení kopírování se zařízení restartuje pomocí nového firmwaru.

    > [!NOTE]
    > Pokud se na obrazovce zobrazí chyby, jako je třeba **Wi-Fi**, je to proto, že DevKit ještě není připojený k Wi-Fi.

1. V DevKit podržte stisknuté **tlačítko b**, nahrajte a uvolněte tlačítko **obnovit** a pak uvolněte **tlačítko b**. Zařízení je teď v režimu přístupového bodu. Na obrazovce se zobrazí obrazovka "IoT DevKit-AP" a IP adresa konfiguračního portálu.

1. V počítači nebo tabletu se připojte k názvu sítě Wi-Fi, který je zobrazený na obrazovce zařízení. Síť Wi-Fi začíná na **AZ-** NÁSLEDOVANÝ adresou MAC. Když se připojíte k této síti, nemáte přístup k Internetu. Tento stav je očekávaný a při konfiguraci zařízení se k této síti budete připojovat jenom po krátké době.

1. Otevřete webový prohlížeč a přejděte na [http://192.168.0.1/](http://192.168.0.1/). Zobrazí se následující webová stránka:

    ![Konfigurační uživatelské rozhraní](media/howto-connect-devkit/config-ui.png)

    Na webové stránce zadejte:

    - Název sítě Wi-Fi (SSID).
    - Vaše heslo k síti Wi-Fi.
    - Podrobnosti připojení: zadejte **ID zařízení**, **Rozsah ID** a **primární klíč SAS** , které jste si poznamenali dříve.

    > [!NOTE]
    > V současné době se IoT DevKit může připojit pouze k Wi-Fi 2,4 GHz, 5 GHz není v důsledku omezení hardwaru podporováno.

1. Vyberte **Konfigurovat zařízení**, zařízení DevKit se restartuje a spustí se aplikace:

    ![Restartovat uživatelské rozhraní](media/howto-connect-devkit/reboot-ui.png)

    Na obrazovce DevKit se zobrazí potvrzení, že aplikace běží:

    ![DevKit spuštěn](media/howto-connect-devkit/devkit-running.png)

DevKit nejprve registruje nové zařízení v aplikaci IoT Central a potom spustí odesílání dat.

## <a name="view-the-telemetry"></a>Zobrazit telemetrii

V tomto kroku zobrazíte telemetrii ve vaší aplikaci Azure IoT Central.

V aplikaci IoT Central vyberte kartu **zařízení** a vyberte zařízení, které jste přidali. Na kartě **Přehled** můžete zobrazit telemetrii ze zařízení DevKit:

![Přehled zařízení IoT Central](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Kontrola kódu

Chcete-li zkontrolovat kód nebo ho upravit a zkompilovat, přejít na [ukázky kódu](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit zařízení DevKit k aplikaci Azure IoT Central, je navržený další krok, ve kterém se dozvíte, jak [nastavit vlastní šablonu zařízení](./howto-set-up-template.md) pro vlastní zařízení IoT.
