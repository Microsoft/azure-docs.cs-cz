---
title: Připojte zařízení za SensorTile.box do aplikace Azure IoT Central | Dokumentace Microsoftu
description: Jako vývojář zařízení zjistěte, jak připojit zařízení SensorTile.box do aplikace Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 8c1b4a4ab834b2203a7e0b6e4e9e366c3fc38774
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65472202"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Připojení zařízení SensorTile.box do aplikace Azure IoT Central

Tento článek popisuje, jak jako vývojář zařízení připojit zařízení SensorTile.box do aplikace Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující prostředky:

* SensorTile.box zařízení. Další informace najdete v tématu [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* ST volit senzor aplikaci nainstalovanou na zařízení s Androidem, můžete [stáhnout odsud](https://play.google.com/store/apps/details?id=com.st.bluems). Další informace naleznete zde: [ST volit senzor](https://www.st.com/stblesensor)
* Azure IoT Central aplikace vytvořené z **DevKits** šablony aplikace. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
* Přidat **SensorTile.box** šablona zařízení do vaší aplikace IoT Central si **šablon** stránku kliknutím na **+ nová**a výběr **SensorTile.box** šablony.

### <a name="get-your-device-connection-details"></a>Získat podrobnosti o připojení zařízení

V aplikaci Azure IoT Central přidat z reálného zařízení **SensorTile.box** šablona zařízení a zaznamenání podrobností o připojení zařízení: **ID rozsahu**, **ID zařízení**, a **primární klíč**:

1. Přidáte zařízení z Device Explorer. Vyberte **+ nový > skutečné** skutečné zařízení přidat.

    * Zadejte malé **ID zařízení**, nebo použijte navržené **ID zařízení**.
    * Zadejte **název zařízení**, nebo použijte navrhovaný název

    ![Přidání zařízení](media/howto-connect-sensortile/real-device.png)

1. Chcete-li získat podrobné informace o připojení zařízení **ID oboru**, **ID zařízení**, a **primární klíč**vyberte **připojit** na stránce zařízení.

    ![Podrobnosti připojení](media/howto-connect-sensortile/connect-device.png)

1. Poznamenejte si podrobnosti o připojení. Dočasně odpojení od Internetu při přípravě zařízení DevKit v dalším kroku.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Nastavit SensorTile.box s mobilní aplikací

V této části se dozvíte, jak nabízet firmware aplikace do zařízení. Můžete pak jak odesílat data zařízení do IoT Central přes mobilní aplikaci ST volit senzor používání připojení Bluetooth s nízkou energie (tabulku).

1. Otevřete ST volit senzor aplikace a stiskněte klávesu **vytvořit novou aplikaci** tlačítko.

    ![Vytvoření aplikace](media/howto-connect-sensortile/create-app.png)

1. Vyberte **měřítko** aplikace.
1. Stisknutím tlačítka Odeslat.

    ![Měřítko nahrávání](media/howto-connect-sensortile/barometer-upload.png)

1. Kliknutím na tlačítko Přehrát přidružené k vaší SensorTile.box.
1. Po dokončení procesu SensorTile.box datových proudů teplota, tlaku a vlhkosti přes zakázat.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Připojení ke cloudu SensorTile.box

V této části se dozvíte, jak připojit SensorTile.box mobilních aplikací a připojit mobilní aplikace do cloudu.

1. V nabídce vlevo vyberte **cloudu protokolování** tlačítko.

    ![Protokolování v cloudu](media/howto-connect-sensortile/cloud-logging.png)

1. Vyberte **Azure IoT Central** jako poskytovatele cloudu.
1. Vložte ID zařízení a ID oboru, který jste si předtím poznamenali.

    ![Přihlašovací údaje](media/howto-connect-sensortile/credentials.png)

1. Vyberte **klíč aplikace** přepínač.
1. Klikněte na tlačítko **připojit** a vyberte telemetrických dat, které chcete nahrát.
1. Po několika sekundách se zobrazí data na řídicí panel aplikací IoT Central.

## <a name="sensortilebox-device-template-details"></a>Podrobnosti o šabloně SensorTile.box zařízení

Aplikace vytvořené z této šablony SensorTile.box zařízení s následujícími charakteristikami:

### <a name="telemetry"></a>Telemetrická data

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| tlak       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | DPS   | -3276   | 3276    | 1              |
| gyroscopeY     | DPS   | -3276   | 3276    | 1              |
| gyroscopeZ     | DPS   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak se připojit k aplikaci Azure IoT Central SensorTile.box, navrhované dalším krokem je další [jak vytvořit šablonu vlastního zařízení](howto-set-up-template.md) pro zařízení IoT.
