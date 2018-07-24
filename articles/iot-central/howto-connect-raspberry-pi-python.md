---
title: Connnect Raspberry Pi až po aplikace Azure IoT Central (Python) | Dokumentace Microsoftu
description: Jako vývojář zařízení jak se připojit na Raspberry Pi do aplikace Azure IoT Central pomocí Pythonu.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: a3d6ad9f2f442481908bc02252fcc8ab1a74419e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205584"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Připojte Raspberry Pi do aplikace Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Tento článek popisuje, jak jako vývojář zařízení připojit Raspberry Pi do aplikace Microsoft Azure IoT Central, pomocí programovacího jazyka Python.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

* Azure IoT Central aplikace vytvořené z **ukázka Devkits** šablony aplikace. Další informace najdete v tématu [vytvoření aplikace Azure IoT Central](howto-create-application.md).
* Raspberry Pi zařízení se systémem Raspbian operačního systému. Potřebujete monitoru, klávesnice a myši připojené k Raspberry Pi přístup k prostředí grafického uživatelského rozhraní. Raspberry Pi musí být schopen [připojení k Internetu](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Volitelně můžete [smysl Hat](https://www.raspberrypi.org/products/sense-hat/) panel doplněk pro Raspberry Pi. Tento panel shromažďuje telemetrická data z různých snímačů a poslat do aplikace Azure IoT Central. Pokud nemáte k dispozici **smysl Hat** panelu, můžete místo toho použít emulátor.

## <a name="sample-devkits-application"></a>**Ukázkový Devkits** aplikace

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **Raspberry Pi** šablona zařízení s následujícími charakteristikami: 

- Telemetrická data, která obsahuje měření pro zařízení **vlhkosti**, **teploty**, **tlak**, **Magnometer** (měřeno podél X Y, Z osy), **Accelorometer** (měří podél X, Y, Z osy) a **volný setrvačník** (měří podél X, Y, osy Z).
- Nastavení zobrazení **napětí**, **aktuální**,**ventilátor rychlost** a **reakcí na Incidenty** přepínací tlačítko.
- Vlastnosti obsahující vlastnosti zařízení **kostka číslo** a **umístění** cloudové vlastnosti.


Najdete všechny podrobnosti o konfiguraci zařízení šablony [Podrobnosti šablony Raspberry PI zařízení](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)
    

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidat z reálného zařízení **Raspberry Pi** šablona zařízení a zkontrolujte poznamenejte si připojovací řetězec zařízení. Další informace najdete v tématu [skutečné zařízení přidat do aplikace Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Konfigurace Raspberry Pi

Následující kroky popisují, jak stáhnout a nakonfigurovat ukázkové aplikace v Pythonu z Githubu. Tato ukázková aplikace:

* Odesílá telemetrická data a hodnoty vlastností do Azure IoT Central.
* Reaguje na změny provedené v Azure IoT Central nastavení.

> [!NOTE]
> Další informace týkající se ukázky Raspberry Pi Pythonu najdete v tématu [Readme](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) soubor na Githubu.

1. Použít webový prohlížeč v Raspberry Pi desktopu přejděte [verzí firmwaru Azure IoT Central](https://github.com/Microsoft/microsoft-iot-central-firmware/releases) stránky.

1. Stáhněte si soubor zip, který obsahuje nejnovější firmware na vaší domovské složky na Raspberry Pi. Název souboru vypadá jako `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Rozbalte soubor firmwaru, použijte **Správce souborů** v desktopu Raspberry Pi. Klikněte pravým tlačítkem na soubor zip a zvolte **extrahovat zde**. Tato operace vytvoří složku s názvem `RaspberryPi-IoTCentral-X.X.X` v domovskou složku.

1. Pokud nemáte k dispozici **smysl Hat** přičleněné k Raspberry Pi, je nutné povolit emulátoru:
    1. V **Správce souborů**v `RaspberryPi-IoTCentral-X.X.X` složky, klikněte pravým tlačítkem na **config.iot** soubor a zvolte **textový Editor**.
    1. Změňte řádek `"simulateSenseHat": false,` k `"simulateSenseHat": true,`.
    1. Uložte změny a zavřete **textový Editor**.

1. Začít **terminálu** relace a použití `cd` příkazu přejděte do složky, kterou jste vytvořili v předchozím kroku.

1. Spuštění ukázkové aplikace spuštěna, zadejte `./start.sh` v **terminálu** okna. Pokud používáte **smysl HAT emulátor**, jeho zobrazí grafické uživatelské rozhraní. Chcete-li změnit hodnoty telemetrických dat odesílaných do aplikace Azure IoT Central, můžete použít grafické uživatelské rozhraní.

1. **Terminálu** okno zobrazí zprávu, která vypadá jako `Device information being served at http://192.168.0.60:8080`. Adresa URL může být jiný ve vašem prostředí. Zkopírujte adresu URL a přejděte na stránku konfigurace pomocí webového prohlížeče:

    ![Konfigurace zařízení](media/howto-connect-raspberry-pi-python/configure.png)

1. Zadejte připojovací řetězec zařízení jste si poznamenali při přidání skutečné zařízení do aplikace Azure IoT Central. Klikněte na tlačítko **konfigurovat zařízení**. Zobrazí se zpráva **zařízení nakonfigurovat, zařízení by měl zahájila odesílání dat do Azure IoT Central okamžik**.

1. V aplikaci Azure IoT Central uvidíte, jak kód spuštěný na Raspberry Pi komunikuje s aplikací:

    * Na **měření** stránky pro skutečné zařízení, můžete zobrazit telemetrická data odesílaná z Raspberry Pi. Pokud používáte **smysl HAT emulátor**, můžete změnit hodnoty telemetrie v grafickém Uživatelském rozhraní na Raspberry Pi.
    * Na **vlastnosti** stránky, zobrazí se hodnota hlášení **kostka číslo** vlastnost.
    * Na **nastavení** stránky, můžete změnit různá nastavení na Raspberry Pi, jako je například napětí a podporuje a rychlost. Když je Raspberry Pi potvrdí změny, nastavení zobrazí jako **synchronizované** v Azure IoT Central.


## <a name="raspberry-pi-device-template-details"></a>Podrobnosti šablony Raspberry PI zařízení

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **Raspberry Pi** šablona zařízení s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 0       | 100     | 0              |
| temp           | ° C     | -40     | 120     | 0              |
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
| PROSTŘEDÍ IR           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka čísla   | dieNumber  | číslo    |
| Text            | Umístění     | location   | neuvedeno       |

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit Raspberry Pi do aplikace Azure IoT Central, tady jsou další navrhované kroky:

* [Připojit obecný klientská aplikace Node.js do Azure IoT Central](howto-connect-nodejs.md)
