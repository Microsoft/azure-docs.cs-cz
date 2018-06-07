---
title: Spojení jednotlivých malin pí do aplikace Azure IoT centrální (Python) | Microsoft Docs
description: Jako vývojář zařízení jak se připojit k Azure IoT centrální aplikace používá Python malin pí.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: e9c2d18a518bd5c98fcc35efdb0dff36970a49b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629061"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Pi malin připojit k aplikaci Azure IoT centrální (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Tento článek popisuje, jak jako vývojář zařízení pro připojení k Microsoft Azure IoT centrální aplikace pomocí programovacího jazyka Python malin pí.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

* Azure IoT centrální aplikace vytvořené pomocí **Devkits ukázka** šablony aplikace. Další informace najdete v tématu [vytvořit aplikaci Azure IoT centrální](howto-create-application.md).
* Malin platformy zařízení se systémem Raspbian operačního systému. Potřebujete monitorování, klávesnice a myši připojené k vaší malin platformy získal přístup k prostředí grafického uživatelského rozhraní. Pi malin musí být schopen [připojení k Internetu](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Volitelně můžete [smysl Hat](https://www.raspberrypi.org/products/sense-hat/) doplněk panelu malin pí. Tento panel shromažďuje telemetrická data z různých senzorů odeslat do aplikace Azure IoT centrální. Pokud nemáte **smysl Hat** Tabule, můžete místo toho použít emulátor.

Aplikace vytvořené z **ukázka Devkits** obsahuje šablony aplikace **malin pí** šablona zařízení s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkosti       | %      | 0       | 100     | 0              |
| dočasné           | ° C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
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
| Napětí      | setVoltage | Intenzita | 0              | 0       | 240     | 0       |
| Aktuální      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Ventilátor rychlosti    | fanSpeed   | OT. / MIN   | 0              | 0       | 1000    | 0       |

Přepnutí nastavení

| Zobrazované jméno | Název pole | Na text | Vypnout text | Počáteční |
| ------------ | ---------- | ------- | -------- | ------- |
| REAKCÍ NA INCIDENTY           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka číslo   | dieNumber  | číslo    |
| Text            | Umístění     | location   | neuvedeno       |

### <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT centrální přidat skutečné zařízení z **malin pí** šablona zařízení a je zaznamenána připojovací řetězec zařízení. Další informace najdete v tématu [přidat skutečné zařízení Azure IoT centrální aplikace](tutorial-add-device.md).

## <a name="configure-the-raspberry-pi"></a>Konfigurace Malinová platformy

Následující kroky popisují, jak stáhnout a nakonfigurovat ukázkovou aplikaci Python z webu GitHub. Tato ukázková aplikace:

* Odešle Azure IoT centrální telemetrie a hodnot vlastností.
* Reaguje na změny provedené v Azure IoT centrální nastavení.

> [!NOTE]
> Další informace o ukázka malin pí Pythonu najdete v tématu [Readme](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) souboru na Githubu.

1. Pomocí webového prohlížeče v malin pí ploše přejděte na [Azure IoT centrální firmware uvolní](https://github.com/Microsoft/microsoft-iot-central-firmware/releases) stránky.

1. Stáhněte si soubor zip, který obsahuje nejnovější firmware do domovské složky na malin pí. Název souboru vypadá `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Dekomprimovat soubor firmwaru, použijte **Správce souborů** v ploše malin pí. Klikněte pravým tlačítkem na soubor zip a zvolte **extrahovat zde**. Tato operace vytvoří složku s názvem `RaspberryPi-IoTCentral-X.X.X` do domovské složky.

1. Pokud nemáte **smysl Hat** Tabule připojené k vaší malin Pi, je nutné povolit emulátoru:
    1. V **správce souborového**v `RaspberryPi-IoTCentral-X.X.X` složku, klikněte pravým tlačítkem myši **config.iot** souboru a zvolte **textového editoru**.
    1. Změňte řádek `"simulateSenseHat": false,` k `"simulateSenseHat": true,`.
    1. Uložte změny a zavřete **textového editoru**.

1. Spuštění **Terminálové** relace a použít `cd` příkaz přejděte do složky, které jste vytvořili v předchozím kroku.

1. Chcete-li začít s ukázkovou aplikaci, zadejte `./start.sh` v **Terminálové** okno. Pokud používáte **smysl HAT emulátoru**, zobrazí jeho grafickým uživatelským rozhraním. Grafické uživatelské rozhraní můžete použít ke změně hodnot telemetrie odeslat do aplikace Azure IoT centrální.

1. **Terminálové** okno zobrazí zprávu, která vypadá jako `Device information being served at http://192.168.0.60:8080`. Adresa URL může být jiný ve vašem prostředí. Zkopírujte adresu URL a použít webový prohlížeč a přejděte na stránku konfigurace:

    ![Konfigurace zařízení](media/howto-connect-raspberry-pi-python/configure.png)

1. Zadejte připojovací řetězec zařízení jste si poznamenali když jste přidali skutečné zařízení k Azure IoT centrální aplikaci. Zvolte **nakonfigurovat zařízení**. Zobrazí se zpráva **zařízení nakonfigurované, zařízení by se měl spustit na okamžik odesílání dat do Azure IoT centrální**.

1. V aplikaci Azure IoT centrální uvidíte, jak kód spuštěný na platformy malin komunikuje s aplikací:

    * Na **měření** stránky pro skutečné zařízení, uvidíte telemetrické zprávy odesílané z malin pí. Pokud používáte **smysl HAT emulátoru**, můžete změnit hodnoty telemetrie v grafickém uživatelském rozhraní na malin pí.
    * Na **vlastnosti** stránky, se zobrazí hodnota hlášení **kostka číslo** vlastnost.
    * Na **nastavení** stránky, můžete změnit různá nastavení na platformy malin například napětí a ventilátor rychlosti. Když pí malin uznává změnu, nastavení zobrazuje jako **synchronizovat** v centrální Azure IoT.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili jak se připojit k aplikaci Azure IoT centrální malin Pi, tady jsou navrhované další kroky:

* [Připojit obecné klienta aplikace Node.js ve službě Azure IoT centrální](howto-connect-nodejs.md)