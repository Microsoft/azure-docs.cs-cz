---
title: Připojte Raspberry Pi do aplikace Azure IoT Central (Python) | Dokumentace Microsoftu
description: Jako vývojář zařízení jak se připojit na Raspberry Pi do aplikace Azure IoT Central pomocí Pythonu.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 6ac16651e2d49dd903ff994b18a8f571bd92fbf6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272355"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Připojte Raspberry Pi do aplikace Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Tento článek popisuje, jak jako vývojář zařízení připojit Raspberry Pi do aplikace Microsoft Azure IoT Central, pomocí programovacího jazyka Python.

## <a name="before-you-begin"></a>Před zahájením

K dokončení kroků v tomto článku, budete potřebovat následující komponenty:

* Azure IoT Central aplikace vytvořené z **ukázka Devkits** šablony aplikace. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
* Raspberry Pi zařízení se systémem Raspbian operačního systému. Raspberry Pi musí být schopný se připojit k Internetu. Další informace najdete v tématu [nastavení Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Ukázkový Devkits** aplikace

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **Raspberry Pi** šablona zařízení s následujícími charakteristikami:

- Telemetrická data, která zahrnuje následující měření, které zařízení bude shromažďovat:
  - Vlhkost
  - Teplota
  - Tlak
  - Magnetometer (X, Y, Z)
  - Akcelerometr (X, Y, Z)
  - Volný setrvačník (X, Y, Z)
- Nastavení
  - Snímač napětí
  - Aktuální
  - Ventilátor rychlost
  - Přepnout reakcí na Incidenty.
- Vlastnosti
  - Kostka vlastnost počtu zařízení
  - Vlastnost Location cloudu

Úplné podrobnosti o konfiguraci zařízení šablony najdete v článku [Podrobnosti šablony Raspberry Pi zařízení](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidat z reálného zařízení **Raspberry Pi** šablona zařízení. Poznamenejte si zařízení, podrobnosti o připojení (**ID oboru**, **ID zařízení**, a **primární klíč**). Další informace najdete v tématu [skutečné zařízení přidat do aplikace Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Konfigurace Raspberry Pi

Následující kroky popisují, jak stáhnout a nakonfigurovat ukázkové aplikace v Pythonu z Githubu. Tato ukázková aplikace:

* Odesílá telemetrická data a hodnoty vlastností do Azure IoT Central.
* Reaguje na změny provedené v Azure IoT Central nastavení.

Ke konfiguraci zařízení, [postupujte podle podrobných pokynů v Githubu](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md).

1. Pokud je nakonfigurovaná zařízení, zařízení spustí odesílání telemetrických dat měření k Azure IoT Central.
1. V aplikaci Azure IoT Central uvidíte, jak kód spuštěný na Raspberry Pi komunikuje s aplikací:

    * Na **měření** stránky pro skutečné zařízení, můžete zobrazit telemetrická data odesílaná z Raspberry Pi.
    * Na **nastavení** stránky, můžete změnit nastavení na Raspberry Pi, jako je například napětí a podporuje a rychlost. Když je Raspberry Pi potvrdí změny, nastavení zobrazí jako **synchronizované**.

## <a name="raspberry-pi-device-template-details"></a>Podrobnosti šablony Raspberry Pi zařízení

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **Raspberry Pi** šablona zařízení s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| tlak       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Nastavení

Číselné nastavení

| Zobrazované jméno | Název pole | Jednotky | Desetinná místa | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Snímač napětí      | setVoltage | Intenzita | 0              | 0       | 240     | 0       |
| Aktuální      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Ventilátor rychlost    | fanSpeed   | OT. / MIN   | 0              | 0       | 1000    | 0       |

Přepínací tlačítko Nastavení

| Zobrazované jméno | Název pole | Na text | Vypnout text | Počáteční |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Type            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka čísla   | dieNumber  | číslo    |
| Text            | Umístění     | location   | neuvedeno       |

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak se připojit k aplikaci Azure IoT Central Raspberry Pi, tady jsou další navrhované kroky:

* [Připojit obecný klientská aplikace Node.js do Azure IoT Central](howto-connect-nodejs.md)
