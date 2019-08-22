---
title: Připojení zařízení SensorTile. Box ke svojí aplikaci Azure IoT Central | Microsoft Docs
description: Jako vývojář zařízení se naučíte připojit zařízení SensorTile. box k aplikaci Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: ce0c5abe6e89094623c07afa2d1c85903e0e7ee7
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877434"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Připojení zařízení SensorTile. box k aplikaci Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Tento článek popisuje, jak jako vývojář zařízení připojit zařízení SensorTile. box k vaší aplikaci Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Před zahájením

K dokončení kroků v tomto článku budete potřebovat následující zdroje:

* Zařízení SensorTile. box. Další informace najdete v tématu [SensorTile. Box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* Aplikace pro senzory ST v systému Android nainstalovaná na zařízení s Androidem si můžete [stáhnout odsud](https://play.google.com/store/apps/details?id=com.st.bluems). Další informace najdete v těchto informacích: [Senzor ST v-TIVOVAT](https://www.st.com/stblesensor)
* Aplikace Azure IoT Central vytvořená v šabloně aplikace **DevKits** Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
* Přidejte šablonu zařízení **SensorTile. Box** do aplikace IoT Central, a to tak, že navštívíte stránku **šablony zařízení** , kliknete na **+ Nový**a vyberete šablonu **SensorTile. Box** .

### <a name="get-your-device-connection-details"></a>Získat podrobnosti o připojení zařízení

V aplikaci Azure IoT Central přidejte reálné zařízení ze šablony zařízení **SensorTile. Box** a poznamenejte si podrobnosti o připojení zařízení: **ID oboru**, **ID zařízení**a **primární klíč**:

1. Přidat zařízení z Device Explorer. Vyberte **+ nový > Real** a přidejte reálné zařízení.

    * Zadejte **ID zařízení**s malým písmenem nebo použijte navržené **ID zařízení**.
    * Zadejte **název zařízení**nebo použijte navrhovaný název.

    ![Přidat zařízení](media/howto-connect-sensortile/real-device.png)

1. Pokud chcete získat podrobnosti o připojení zařízení, **ID oboru**, **ID zařízení**a **primární klíč**, vyberte **připojit** na stránce zařízení.

    ![Podrobnosti připojení](media/howto-connect-sensortile/connect-device.png)

1. Poznamenejte si podrobnosti o připojení. Po přípravě zařízení DevKit v dalším kroku budete dočasně odpojeni od Internetu.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Nastavení SensorTile. Box mobilní aplikací

V této části se dozvíte, jak odeslat firmware aplikace do zařízení. Pak můžete odesílat data zařízení IoT Central prostřednictvím mobilní aplikace senzoru ST. v programu pro připojení k Internetu pomocí technologie Bluetooth (s nízkou spotřebou).

1. Otevřete aplikaci pro senzory ST BULKu a stiskněte tlačítko **vytvořit novou aplikaci** .

    ![Vytvořit aplikaci](media/howto-connect-sensortile/create-app.png)

1. Vyberte aplikaci **Barometer** .
1. Stiskněte tlačítko nahrát.

    ![Nahrávání Barometer](media/howto-connect-sensortile/barometer-upload.png)

1. Stiskněte tlačítko Přehrát přidružené k vašemu SensorTile. box.
1. Po dokončení procesu SensorTile. Box vysílá teplotu, tlak a vlhkost přes tabulku.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Připojení SensorTile. Box ke cloudu

V této části se dozvíte, jak připojit SensorTile. box do mobilní aplikace a připojit mobilní aplikaci ke cloudu.

1. V nabídce vlevo vyberte tlačítko protokolování do **cloudu** .

    ![Protokolování cloudu](media/howto-connect-sensortile/cloud-logging.png)

1. Jako poskytovatele cloudu vyberte **Azure IoT Central** .
1. Vložte ID zařízení a ID oboru, které jste si poznamenali dříve.

    ![Pověření](media/howto-connect-sensortile/credentials.png)

1. Vyberte přepínač **klíče aplikace** .
1. Klikněte na **připojit** a vyberte data telemetrie, která chcete nahrát.
1. Po několika sekundách se data zobrazí na řídicím panelu aplikace IoT Central.

## <a name="sensortilebox-device-template-details"></a>Podrobnosti o šabloně zařízení SensorTile. box

Aplikace vytvořená v šabloně zařízení SensorTile. box s následujícími charakteristikami:

### <a name="telemetry"></a>Telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 30       | 90     | 1              |
| názvem           | °C     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg/Nm3     | -2000   | 2000    | 0              |
| akcelerometr | mg/Nm3     | -2000   | 2000    | 0              |
| accelerometerZ | mg/Nm3     | -2000   | 2000    | 0              |
| gyroscopeX     | DPS   | -3276   | 3276    | 1              |
| gyroscopeY     | DPS   | -3276   | 3276    | 1              |
| gyroscopeZ     | DPS   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit SensorTile. box do vaší aplikace IoT Central Azure, je navržený další krok, kde se dozvíte, [jak nastavit vlastní šablonu zařízení](howto-set-up-template.md) pro vlastní zařízení IoT.
