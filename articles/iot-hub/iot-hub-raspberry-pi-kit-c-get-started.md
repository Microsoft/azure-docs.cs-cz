---
title: Připojení Raspberry Pi k Azure IoT Hub pomocí C | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nastavit a připojit Raspberry Pi k Azure IoT Hub pro Raspberry Pi k odesílání dat na cloudovou platformu Azure
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 94ac75c4165b11e343ce5c31480a511ebf978a36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67838782"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Připojení Raspberry Pi k Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu začnete tím, že se naučíte základy práce s Raspberry Pi, který běží Raspbian. Pak se dozvíte, jak bez problémů připojit vaše zařízení ke cloudu pomocí [Služby Azure IoT Hub](about-iot-hub.md). Ukázky jádra IoT core pro Windows 10 najdete v [Centru pro windows dev .](https://www.windowsondevices.com/)

Ještě nemáte soupravu? Zkuste [Raspberry Pi online simulátor](iot-hub-raspberry-pi-web-simulator-get-started.md). Nebo koupit novou soupravu [zde](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Co děláte

* Vytvořte centrum IoT.

* Zaregistrujte zařízení pro Pi ve svém centru IoT.

* Nastavení Raspberry Pi.

* Spusťte ukázkovou aplikaci na Pi a odešlete data senzorů do centra IoT hub.

Připojte Raspberry Pi k centru IoT, které vytvoříte. Poté spustíte ukázkovou aplikaci na Pi, abyste shromáždili údaje o teplotě a vlhkosti ze senzoru BME280. Nakonec odešlete data senzoru do centra IoT hub.

## <a name="what-you-learn"></a>Co se naučíte

* Jak vytvořit azure iot hub a získat nový řetězec připojení zařízení.

* Jak připojit Pi se snímačem BME280.

* Jak shromažďovat data senzorů spuštěním ukázkové aplikace na Pi.

* Jak odeslat data ze senzorů do centra IoT.

## <a name="what-you-need"></a>Co potřebujete

![Co potřebujete](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Deska Raspberry Pi 2 nebo Raspberry Pi 3.

* Aktivní předplatné Azure. Pokud nemáte účet Azure, [vytvořte si bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) během několika minut.

* Monitor, klávesnice USB a myš, které se připojují k Pi.

* Mac nebo počítač se systémem Windows nebo Linux.

* Připojení k Internetu.

* 16 GB nebo vyšší karta microSD.

* Adaptér USB-SD nebo karta microSD pro vypálení bitové kopie operačního systému na kartu microSD.

* 5-volt 2-amp napájecí zdroj s 6-noha micro USB kabel.

Následující položky jsou volitelné:

* Sestavený snímač teploty, tlaku a vlhkosti Adafruit BME280.

* Prkénkem.

* 6 f/m propojovacích vodičů.

* Rozptýlená 10mm LED.

> [!NOTE]
> Tyto položky jsou volitelné, protože ukázka kódu podporuje simulovaná data senzoru.
>

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Nastavit Raspberry Pi

Nyní nastavte Raspberry Pi.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalace operačního systému Raspbian pro Pi

Připravte kartu microSD pro instalaci raspbianimage.

1. Stáhnout Raspbian.

   1. [Stáhnout Raspbian Stretch s Desktop](https://www.raspberrypi.org/downloads/raspbian/) (soubor ZIP).

   2. Extrahujte raspbianský obraz do složky v počítači.

2. Nainstalujte Raspbian na kartu microSD.

   1. [Stáhněte a nainstalujte nástroj pro vypalovačku karet Etcher SD](https://etcher.io/).

   2. Spusťte Etcher a vyberte raspbian obraz, který jste extrahovali v kroku 1.

   3. Vyberte jednotku karty microSD. Všimněte si, že Etcher již možná vybral správný disk.

   4. Klepnutím na tlačítko Flash nainstalujte raspbian na kartu microSD.

   5. Po dokončení instalace vyjměte kartu microSD z počítače. Je bezpečné vyjmout kartu microSD přímo, protože Etcher automaticky vysune nebo odpojí kartu microSD po dokončení.

   6. Vložte kartu microSD do pi.

### <a name="enable-ssh-and-spi"></a>Povolit SSH a SPI

1. Připojte Pi k monitoru, klávesnici a myši, spusťte Pi `pi` a přihlaste se k Raspbian pomocí uživatelského jména a `raspberry` jako heslo.
 
2. Klikněte na ikonu Raspberry > **Předvolby** > **Raspberry Pi Configuration**.

   ![Nabídka Raspbian Preferences](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Na kartě **Rozhraní** nastavte **spi** a **SSH** na **povolit**a klepněte na tlačítko **OK**. Pokud nemáte fyzické senzory a chcete použít simulovaná data senzorů, je tento krok volitelný.

   ![Povolit SPI a SSH na Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Chcete-li povolit SSH a SPI, můžete najít další referenční dokumenty na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) a [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Připojte senzor k Pi

Pomocí prkénku a propojovacích vodičů připojte k Pi LED a BME280 takto. Pokud senzor nemáte, [přeskočte tuto část](#connect-pi-to-the-network).

![Připojení Raspberry Pi a senzoru](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

Snímač BME280 může shromažďovat údaje o teplotě a vlhkosti. A LED dioda bude blikat, pokud dojde ke komunikaci mezi zařízením a cloudem.

Pro kolíky senzorů použijte následující kabeláž:

| Start (& LED senzor)     | Konec (deska)            | Barva kabelu   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (Pin 5G)         | GPIO 4 (kolík 7)         | Bílý kabel   |
| LED GND (Pin 6G)         | GND (Kolík 6)            | Černý kabel   |
| VDD (kolík 18F)            | 3.3V PWR (pin 17)      | Bílý kabel   |
| GND (Pin 20F)            | GND (Kolík 20)           | Černý kabel   |
| SCK (Kolík 21F)            | SPI0 SCLK (kolík 23)     | Oranžový kabel  |
| SDO (pin 22F)            | SPI0 MISO (kolík 21)     | Žlutý kabel  |
| SDI (pin 23F)            | SPI0 MOSI (kolík 19)     | Zelený kabel   |
| CS (Pin 24F)             | SPI0 CS (Kolík 24)       | Modrý kabel    |

Klikněte pro zobrazení [Raspberry Pi 2 & 3 Pin mapování](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) pro váš odkaz.

Po úspěšném připojení BME280 k raspberry pi by to mělo být jako pod obrázkem.

![Připojené Pí a BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Připojení Pi k síti

Zapněte Pi pomocí kabelu micro USB a napájecího zdroje. Pomocí ethernetového kabelu připojte Pi k kabelové síti nebo postupujte [podle pokynů nadace Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) pro připojení Pi k bezdrátové síti. Po úspěšném připojení pi k síti je třeba poznamenat [IP adresu vašeho Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Připojení k kabelové síti](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Spuštění ukázkové aplikace na Pi

### <a name="sign-into-your-raspberry-pi"></a>Přihlaste se do svého Raspberry Pi

1. Použijte jeden z následujících klientů SSH z hostitelského počítače pro připojení k Raspberry Pi.
   
   **Uživatelé systému Windows**
   1. Stáhněte a nainstalujte [PuTTY](https://www.putty.org/) pro Windows. 
   1. Zkopírujte IP adresu vašeho Pi do oddílu Název hostitele (nebo IP adresa) a jako typ připojení vyberte SSH.
   
   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Uživatelé Macu a Ubuntu**

   Použijte vestavěného klienta SSH na Ubuntu nebo macOS. Možná budete muset `ssh pi@<ip address of pi>` spustit pro připojení Pi přes SSH.
   > [!NOTE]
   > Výchozí uživatelské jméno `pi` je a `raspberry`heslo je .


### <a name="configure-the-sample-application"></a>Konfigurace ukázkové aplikace

1. Klonujte ukázkovou aplikaci spuštěním následujícího příkazu:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Spustit instalační skript:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Pokud **nemáte fyzický BME280**, můžete použít '--simulovaná data' jako parametr příkazového řádku k simulaci teploty&vlhkosti. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Sestavení a spuštění ukázkové aplikace

1. Vytvořte ukázkovou aplikaci spuštěním následujícího příkazu:

   ```bash
   cmake . && make
   ```
   
   ![Vytvořit výstup](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Spusťte ukázkovou aplikaci spuštěním následujícího příkazu:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Ujistěte se, že zkopírujete a vložíte připojovací řetězec zařízení do jednotlivých uvozovek.
   >

Měl by se zobrazit následující výstup s daty snímače a zprávami posílanými do vašeho centra IoT Hub.

![Výstup – data snímače odesílaná z Raspberry Pi do centra IoT](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Čtení zpráv přijatých vaším centrem

Jedním ze způsobů, jak sledovat zprávy přijaté vaším centrem IoT hub z vašeho zařízení, je použití nástroje Azure IoT Tools for Visual Studio Code. Další informace najdete [v tématu Použití nástrojů Azure IoT pro kód Visual Studia k odesílání a přijímání zpráv mezi zařízením a službou IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Další způsoby zpracování dat odeslaných zařízením najdete v další části.

## <a name="next-steps"></a>Další kroky

Spouštěli jste ukázkovou aplikaci pro shromažďování dat senzorů a jejich odeslání do služby IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
