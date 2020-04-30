---
title: Připojení zařízení DevKit k aplikaci Azure IoT Central | Microsoft Docs
description: Jako vývojář zařízení se naučíte připojit zařízení IoT DevKit MXChip k aplikaci Azure IoT Central pomocí technologie Plug and Play IoT (Preview).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81756812"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Připojení zařízení IoT DevKit MXChip k aplikaci Azure IoT Central

*Tento článek se týká vývojářů zařízení.*

V tomto článku se dozvíte, jak připojit zařízení MXChip IoT DevKit (DevKit) k aplikaci Azure IoT Central. Zařízení používá pro zařízení DevKit certifikovaný model Certified IoT technologie Plug and Play (Preview), aby se nakonfigurovalo připojení k IoT Central.

V tomto článku s postupem:

- Získejte podrobnosti o připojení z vaší aplikace IoT Central.
- Připravte zařízení a připojte ho k aplikaci IoT Central.
- Zobrazení telemetrie a vlastností ze zařízení v IoT Central.

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto článku budete potřebovat následující:

- [Zařízení DevKit](https://aka.ms/iot-devkit-purchase).
- Aplikace IoT Central. Můžete postupovat podle kroků v části [Vytvoření aplikace IoT Central](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Získat podrobnosti o připojení zařízení

1. V aplikaci Azure IoT Central vyberte kartu **šablony zařízení** a vyberte **+ Nová**. V části **použít předem nakonfigurovanou šablonu zařízení**vyberte **MXChip IoT DevKit**.

    ![Šablona zařízení pro MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Vyberte **Další: přizpůsobení** a pak **vytvořit**.

1. Vyberte kartu **zařízení** . V seznamu zařízení vyberte **MXChip IoT DevKit** a vyberte **+ Nový** , aby se v šabloně vytvořilo nové zařízení.

    ![Nové zařízení](media/howto-connect-devkit/new-device.png)

1. V automaticky otevíraném okně zadejte **ID zařízení** `SampleDevKit` a jako **název zařízení** `MXChip IoT DevKit - Sample`. Ujistěte se, že je **simulovaná** možnost vypnutá. Pak vyberte **vytvořit**.

    ![ID a název zařízení](media/howto-connect-devkit/device-id-name.png)

1. Vyberte zařízení, které jste vytvořili, a pak vyberte **připojit**. Poznamenejte si **Rozsah ID**, **ID zařízení**a **primární klíč**. Tyto hodnoty budete potřebovat později v tomto článku s postupem.

    ![Informace o připojení zařízení](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Příprava zařízení

1. Stáhněte si nejnovější [předem sestavený firmware služby Azure IoT Central technologie Plug and Play (Preview)](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) pro zařízení DevKit z GitHubu.

1. Připojte zařízení DevKit k vývojovému počítači pomocí kabelu USB. V systému Windows se otevře okno Průzkumník souborů na jednotce namapované na úložiště na zařízení DevKit. Například jednotka může být volána **AZ3166 (D:)**.

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
    - Podrobnosti připojení: zadejte **ID zařízení**, **Rozsah ID**a **primární klíč SAS** , které jste si poznamenali dříve.

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

Pokud jste vývojářem zařízení, některé z navrhovaných dalších kroků:

- Přečtěte si o [připojení zařízení v Azure IoT Central](./concepts-get-connected.md)
- Naučte se [monitorovat připojení zařízení pomocí Azure CLI](./howto-monitor-devices-azure-cli.md) .
