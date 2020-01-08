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
ms.openlocfilehash: 3daa567a916bd0abeb407028c7d06bd1f2bd464b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454074"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Připojení k aplikaci Azure IoT Central k aplikaci v jazyce malin – PI (Python)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Tento článek popisuje, jak jako vývojář pro zařízení připojit k aplikaci Microsoft Azure IoT Central aplikaci pomocí programovacího jazyka Python.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující komponenty:

* Aplikace Azure IoT Central vytvořená ze šablony **starší verze aplikace** . Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
* Zařízení malinu PI, na kterém běží operační systém Raspbian. Malina. PI musí být schopná připojit se k Internetu. Další informace najdete v tématu [nastavení svého malinu PI](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

> [!TIP]
> Další informace o nastavení a připojení k zařízení malin. PI najdete na webu Začínáme [s malinou PI](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi) .

## <a name="add-a-device-template"></a>Přidání šablony zařízení

V aplikaci Azure IoT Central přidejte novou šablonu zařízení **malin. PI** s následujícími charakteristikami:

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
  - Rychlost větráku
  - Přepínač IR
- Vlastnosti
  - Vlastnost zařízení číslo pro Die
  - Umístění vlastnosti cloudu

1. Z šablon zařízení ![vyberte **+ nový**](media/howto-connect-raspberry-pi-python/adddevicetemplate.png)
   

2. Vyberte **maliny PI** a vytvořte šablonu zařízení malin. PI ![přidat šablonu zařízení](media/howto-connect-raspberry-pi-python/newdevicetemplate.png)

Úplné podrobnosti o konfiguraci šablony zařízení najdete v [podrobnostech o šabloně zařízení malin. PI](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidejte reálné zařízení ze šablony zařízení malin. **Pi** . Poznamenejte si podrobnosti o připojení zařízení (**ID oboru**, **ID zařízení**a **primární klíč**). Další informace najdete v tématu [Přidání reálného zařízení do aplikace Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Konfigurace maliny v PI

Následující postup popisuje, jak stáhnout a nakonfigurovat ukázkovou aplikaci v Pythonu z GitHubu. Tato ukázková aplikace:

* Odesílá hodnoty telemetrie a vlastností do Azure IoT Central.
* Reaguje na nastavení změn provedených v Azure IoT Central.

1. Připojte se k prostředí prostředí v aplikaci malin PI, a to buď z plochy Malina, nebo vzdáleně pomocí SSH.

1. Spuštěním následujícího příkazu nainstalujte klienta IoT Central Python:

    ```bash
    pip install iotc
    ```

1. Stáhněte si vzorový kód Pythonu:

    ```bash
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. Upravte soubor `app.py`, který jste stáhli, a nahraďte zástupné symboly `DEVICE_ID`, `SCOPE_ID`a `PRIMARY/SECONDARY device KEY` hodnotami připojení, které jste si poznamenali dříve. Uložte provedené změny.

    > [!TIP]
    > V prostředí v aplikaci malin PI můžete použít textové editory **nano** nebo **VI** .

1. Pomocí následujícího příkazu spusťte ukázku:

    ```bash
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
| temp           | °C     | -40     | 120     | 0              |
| tlak       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1 000    | 0              |
| magnetometerY  | mgauss | -1000   | 1 000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1 000    | 0              |
| accelerometerX | mg/Nm3     | -2000   | 2000    | 0              |
| accelerometerY | mg/Nm3     | -2000   | 2000    | 0              |
| accelerometerZ | mg/Nm3     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Nastavení

Číselná nastavení

| Zobrazované jméno | Název pole | Jednotky | Desetinná místa | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napájení      | setVoltage | V voltech | 0              | 0       | 240     | 0       |
| Aktuální      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Rychlost větráku    | fanSpeed   | /MIN   | 0              | 0       | 1 000    | 0       |

Přepnout nastavení

| Zobrazované jméno | Název pole | Na text | Vypnuto textu | Počáteční |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Data type |
| --------------- | ------------ | ---------- | --------- |
| Vlastnost zařízení | Číslo Die   | dieNumber  | číslo    |
| Text            | Umístění     | location   | Nevztahuje se       |

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit malinu PI ke svojí aplikaci IoT Central Azure, je doporučeným dalším krokem postup pro [nastavení vlastní šablony zařízení](howto-set-up-template.md) pro vlastní zařízení IoT.
