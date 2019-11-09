---
title: Připojení zařízení DevKit k aplikaci Azure IoT Central | Microsoft Docs
description: Jako vývojář zařízení se naučíte připojit zařízení IoT DevKit MXChip k aplikaci Azure IoT Central pomocí technologie Plug and Play IoT.
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 7d8ed5ebde946134bc7e4779ec79c9ea853a6ef4
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895186"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Připojení zařízení IoT DevKit MXChip k aplikaci Azure IoT Central

V tomto článku se dozvíte, jak připojit zařízení MXChip IoT DevKit (DevKit) k aplikaci Azure IoT Central. Zařízení používá pro zařízení DevKit certifikovaný model IoT technologie Plug and Play ke konfiguraci připojení k IoT Central.

V tomto článku s postupem:

- Získejte podrobnosti o připojení z vaší aplikace IoT Central.
- Připravte zařízení a připojte ho k aplikaci IoT Central.
- Zobrazení telemetrie a vlastností ze zařízení v IoT Central.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující zdroje:

- [Zařízení DevKit](https://aka.ms/iot-devkit-purchase).
- Aplikace IoT Central vytvořená z šablony **aplikace ve verzi Preview** . Můžete postupovat podle kroků v části [Vytvoření aplikace IoT technologie Plug and Play](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Získat podrobnosti o připojení zařízení

V aplikaci Azure IoT Central vyberte kartu **Správa** a vyberte **připojení zařízení**. Poznamenejte si **ID oboru** a **primární klíč** (v odkazu **Zobrazit klíče** ). A ujistěte se, že je povoleno **automatické schvalování** .

![Podrobnosti o připojení skupiny zařízení](media/howto-connect-devkit/device-group-connection-details.png)

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
    - Podrobnosti o připojení: **ID zařízení** , které si můžete vybrat sami, a v **primárním klíči** s **ID oboru** a skupinou SAS, které jste si poznamenali dříve.

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

Chcete-li zkontrolovat kód nebo ho upravit a zkompilovat, přejít do [úložiště GitHub MXChip IoT DevKit Sample Code](https://github.com/MXCHIP/IoTDevKit/tree/master/pnp).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit zařízení DevKit k aplikaci Azure IoT Central, je navržený další krok, ve kterém se dozvíte, jak [nastavit vlastní šablonu zařízení](./howto-set-up-template.md) pro vlastní zařízení IoT.
