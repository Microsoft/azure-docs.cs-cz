---
title: Připojení k aplikaci Azure IoT Central (Python) ve složce malin | Microsoft Docs
description: Jako vývojář zařízení, jak připojit malinovou pi k aplikaci Azure IoT Central pomocí Pythonu.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3f91e09e35eec0685cb4333802b860fb08412cb6
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019745"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Připojení k aplikaci Azure IoT Central k aplikaci v jazyce malin – PI (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Tento článek popisuje, jak jako vývojář pro zařízení připojit k aplikaci Microsoft Azure IoT Central aplikaci pomocí programovacího jazyka Python.

## <a name="before-you-begin"></a>Před zahájením

K dokončení kroků v tomto článku budete potřebovat následující komponenty:

* Aplikace IoT Central v Azure vytvořená z **ukázkové** šablony aplikace Devkits Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
* Zařízení malinu PI, na kterém běží operační systém Raspbian. Malina. PI musí být schopná připojit se k Internetu. Další informace najdete v tématu [nastavení svého malinu PI](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

> [!TIP]
> Další informace o nastavení a připojení k zařízení malin. PI najdete na webu Začínáme [s malinou PI](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi) .

## <a name="sample-devkits-application"></a>**Ukázková aplikace Devkits**

Aplikace vytvořená z ukázkové šablony aplikace **Devkits** zahrnuje šablonu zařízení **Malina v PI** s následujícími charakteristikami:

- Telemetrii, která zahrnuje následující měření, která bude zařízení shromažďovat:
  - Vlhkost
  - Teplota
  - Tlak
  - Magnetometer (X, Y, Z)
  - Akcelerometr (X, Y, Z)
  - Vybavený gyroskopem (X, Y, Z)
- Nastavení
  - Napájení
  - Aktuální
  - Rychlost ventilátoru
  - Přepínač IR
- Vlastnosti
  - Vlastnost zařízení číslo pro Die
  - Umístění vlastnosti cloudu

Úplné podrobnosti o konfiguraci šablony zařízení najdete v podrobnostech o [šabloně zařízení malin. PI](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidejte reálné zařízení ze šablony zařízení malin. **Pi** . Poznamenejte si podrobnosti o připojení zařízení (**ID oboru**, **ID zařízení**a **primární klíč**). Další informace najdete v tématu [Přidání reálného zařízení do aplikace Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Konfigurace maliny v PI

Následující postup popisuje, jak stáhnout a nakonfigurovat ukázkovou aplikaci v Pythonu z GitHubu. Tato ukázková aplikace:

* Odesílá hodnoty telemetrie a vlastností do Azure IoT Central.
* Reaguje na nastavení změn provedených v Azure IoT Central.

1. Připojte se k prostředí prostředí v aplikaci malin PI, a to buď z plochy Malina, nebo vzdáleně pomocí SSH.

1. Spuštěním následujícího příkazu nainstalujte klienta IoT Central Python:

    ```sh
    pip install iotc
    ```

1. Stáhněte si vzorový kód Pythonu:

    ```sh
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. Upravte soubor, který jste stáhli, `PRIMARY/SECONDARY device KEY` a `SCOPE_ID`zástupné symboly nahraďte hodnotami připojení, které jste si poznamenali dříve. `DEVICE_ID` `app.py` Uložte provedené změny.

    > [!TIP]
    > V prostředí v aplikaci malin PI můžete použít textové editory **nano** nebo **VI** .

1. Pomocí následujícího příkazu spusťte ukázku:

    ```sh
    python app.py
    ```

    V aplikaci malin PI začíná posílání měření telemetrie do Azure IoT Central.

1. V aplikaci Azure IoT Central můžete vidět, jak kód spuštěný ve Malině PI komunikuje s aplikací:

    * Na stránce **měření** reálného zařízení uvidíte telemetrii odeslanou od Malin. PI.
    * Na stránce **vlastnosti** můžete zobrazit vlastnost zařízení **číslo** .
    * Na stránce **Nastavení** můžete změnit nastavení pro malinu PI, jako je například napětí a rychlost ventilátoru. Když aplikace malin. PI tuto změnu potvrdí, nastavení se zobrazí jako **synchronizované**.

## <a name="raspberry-pi-device-template-details"></a>Podrobnosti šablony zařízení malin. PI

Aplikace vytvořená z ukázkové šablony aplikace **Devkits** zahrnuje šablonu zařízení **Malina v PI** s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 0       | 100     | 0              |
| názvem           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg/Nm3     | -2000   | 2000    | 0              |
| akcelerometr | mg/Nm3     | -2000   | 2000    | 0              |
| accelerometerZ | mg/Nm3     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Nastavení

Číselná nastavení

| Display name | Název pole | Jednotky | Desetinná místa | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napájení      | setVoltage | V voltech | 0              | 0       | 240     | 0       |
| Aktuální      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Rychlost ventilátoru    | fanSpeed   | /MIN   | 0              | 0       | 1000    | 0       |

Přepnout nastavení

| Display name | Název pole | Na text | Vypnuto textu | Počáteční |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ZAPNUTO      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| type            | Display name | Název pole | Datový typ |
| --------------- | ------------ | ---------- | --------- |
| Vlastnost zařízení | Číslo Die   | dieNumber  | číslo    |
| Text            | Location     | location   | Není k dispozici       |

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit malinu PI ke svojí aplikaci IoT Central Azure, je doporučeným dalším krokem postup pro [nastavení vlastní šablony zařízení](howto-set-up-template.md) pro vlastní zařízení IoT.
