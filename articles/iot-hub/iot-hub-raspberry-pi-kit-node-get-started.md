---
title: Připojení Raspberry Pi k Azure IoT Hub v cloudu (Node.js)
description: V tomto kurzu se dozvíte, jak nastavit a připojit Raspberry Pi k Azure IoT Hub pro Raspberry Pi k odesílání dat na cloudovou platformu Azure.
author: wesmc7777
manager: eliotgra
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi posílat data do cloudu, malina pi do cloudu
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.openlocfilehash: 3175956e35603cc4ad3a938f3d316c0af8f2d227
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640532"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Připojení Raspberry Pi k Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu začnete tím, že se naučíte základy práce s Raspberry Pi, který běží Raspbian. Pak se dozvíte, jak bez problémů připojit vaše zařízení ke cloudu pomocí [Služby Azure IoT Hub](about-iot-hub.md). Ukázky jádra IoT core pro Windows 10 najdete v [Centru pro windows dev .](https://www.windowsondevices.com/)

Ještě nemáte soupravu? Zkuste [Raspberry Pi online simulátor](iot-hub-raspberry-pi-web-simulator-get-started.md). Nebo koupit novou soupravu [zde](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Co děláte

* Vytvořte centrum IoT.

* Zaregistrujte zařízení pro Pi ve svém centru IoT.

* Nastavte Raspberry Pi.

* Spusťte ukázkovou aplikaci na Pi a odešlete data senzorů do centra IoT hub.

## <a name="what-you-learn"></a>Co se naučíte

* Jak vytvořit azure iot hub a získat nový řetězec připojení zařízení.

* Jak připojit Pi se snímačem BME280.

* Jak shromažďovat data senzorů spuštěním ukázkové aplikace na Pi.

* Jak odeslat data ze senzorů do centra IoT.

## <a name="what-you-need"></a>Co potřebujete

![Co potřebujete](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Deska Raspberry Pi 2 nebo Raspberry Pi 3.

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

* Monitor, klávesnice USB a myš, která se připojuje k Pi.

* Mac nebo PC se systémem Windows nebo Linux.

* Připojení k internetu.

* 16 GB nebo vyšší karta microSD.

* Adaptér USB-SD nebo karta microSD pro vypálení bitové kopie operačního systému na kartu microSD.

* 5-volt 2-amp napájecí zdroj s 6-noha micro USB kabel.

Následující položky jsou volitelné:

* Sestavený snímač teploty, tlaku a vlhkosti Adafruit BME280.

* Prkénkem.

* 6 f/m propojovacích vodičů.

* Rozptýlená 10mm LED.

> [!NOTE]
> Pokud nemáte volitelné položky, můžete použít simulovaná data senzorů.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Nastavit Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalace operačního systému Raspbian pro Pi

Připravte kartu microSD pro instalaci raspbianimage.

1. Stáhnout Raspbian.

   a. [Raspbian Buster s desktopem](https://www.raspberrypi.org/downloads/raspbian/) (soubor ZIP).

   b. Extrahujte raspbianský obraz do složky v počítači.

2. Nainstalujte Raspbian na kartu microSD.

   a. [Stáhněte a nainstalujte nástroj pro vypalovačku karet Etcher SD](https://etcher.io/).

   b. Spusťte Etcher a vyberte raspbian obraz, který jste extrahovali v kroku 1.

   c. Vyberte jednotku karty microSD. Etcher možná již zvolil správný disk.

   d. Klepnutím na tlačítko Flash nainstalujte raspbian na kartu microSD.

   e. Po dokončení instalace vyjměte kartu microSD z počítače. Je bezpečné vyjmout kartu microSD přímo, protože Etcher automaticky vysune nebo odpojí kartu microSD po dokončení.

   f. Vložte kartu microSD do pi.

### <a name="enable-ssh-and-i2c"></a>Povolit SSH a I2C

1. Připojte Pi k monitoru, klávesnici a myši.

2. Spusťte Pi a pak se `pi` přihlaste k `raspberry` Raspbian pomocí jako uživatelské jméno a jako heslo.

3. Klikněte na ikonu Raspberry > **Předvolby** > **Raspberry Pi Configuration**.

   ![Nabídka Raspbian Preferences](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. Na kartě Rozhraní nastavte **položky** **I2C** a **SSH** na **povolit**a klepněte na tlačítko **OK**. Pokud nemáte fyzické senzory a chcete použít simulovaná data senzorů, je tento krok volitelný.

   ![Povolit I2C a SSH na Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Chcete-li povolit SSH a I2C, můžete najít další referenční dokumenty o [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) a [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Připojte senzor k Pi

Pomocí prkénku a propojovacích vodičů připojte k Pi LED a BME280 takto. Pokud senzor nemáte, [přeskočte tuto část](#connect-pi-to-the-network).

![Připojení Raspberry Pi a senzoru](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

Snímač BME280 může shromažďovat údaje o teplotě a vlhkosti. Led dioda bliká, když zařízení odešle zprávu do cloudu.

Pro kolíky senzorů použijte následující kabeláž:

| Start (& LED senzor)     | Konec (deska)            | Barva kabelu   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5G)             | 3.3V PWR (pin 1)       | Bílý kabel   |
| GND (Pin 7G)             | GND (Kolík 6)            | Hnědý kabel   |
| SDI (kolík 10g)            | I2C1 SDA (kolík 3)       | Červený kabel     |
| SCK (Kolík 8g)             | I2C1 SCL (kolík 5)       | Oranžový kabel  |
| LED VDD (pin 18F)        | GPIO 24 (kolík 18)       | Bílý kabel   |
| LED GND (pin 17F)        | GND (Kolík 20)           | Černý kabel   |

Klikněte pro zobrazení [Raspberry Pi 2 & 3 pin mapování](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) pro váš odkaz.

Po úspěšném připojení BME280 k raspberry pi by to mělo být jako pod obrázkem.

![Připojené Pí a BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Připojení Pi k síti

Zapněte Pi pomocí kabelu micro USB a napájecího zdroje. Pomocí ethernetového kabelu připojte Pi k kabelové síti nebo postupujte [podle pokynů nadace Raspberry Pi Foundation](https://www.raspberrypi.org/documentation/configuration/wireless/) pro připojení Pi k bezdrátové síti. Po úspěšném připojení pi k síti je třeba poznamenat [IP adresu vašeho Pi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Připojení k kabelové síti](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Zkontrolujte, zda je pi připojenke stejné síti jako počítač. Pokud je například počítač připojen k bezdrátové síti, zatímco pi je připojen k kabelové síti, nemusí se ve výstupu devdisco zobrazit ip adresa.

## <a name="run-a-sample-application-on-pi"></a>Spuštění ukázkové aplikace na Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Klonovat ukázkovou aplikaci a nainstalovat požadované balíčky

1. Připojte se k Raspberry Pi s jedním z následujících klientů SSH z hostitelského počítače:

   **Uživatelé systému Windows**

   a. Stáhněte a nainstalujte [PuTTY](https://www.putty.org/) pro Windows.

   b. Zkopírujte IP adresu vašeho Pi do oddílu Název hostitele (nebo IP adresa) a jako typ připojení vyberte SSH.

   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Uživatelé Macu a Ubuntu**

   Použijte vestavěného klienta SSH na Ubuntu nebo macOS. Možná budete muset `ssh pi@<ip address of pi>` spustit pro připojení Pi přes SSH.

   > [!NOTE]
   > Výchozí uživatelské jméno `pi` je a `raspberry`heslo je .

2. Nainstalujte node.js a NPM do svého Pi.

   Nejprve zkontrolujte verzi Node.js.

   ```bash
   node -v
   ```

   Pokud je verze nižší než 10.x nebo pokud na vašem Pi není žádný soubor Node.js, nainstalujte nejnovější verzi.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Klonujte ukázkovou aplikaci.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Nainstalujte všechny balíčky pro ukázku. Instalace zahrnuje Azure IoT zařízení SDK, Knihovna snímače BME280 a knihovna Wiring Pi.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >Dokončení procesu instalace může trvat několik minut v závislosti na síťovém připojení.

### <a name="configure-the-sample-application"></a>Konfigurace ukázkové aplikace

1. Otevřete konfigurační soubor spuštěním následujících příkazů:

   ```bash
   nano config.json
   ```

   ![Konfigurační soubor](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   V tomto souboru jsou dvě položky, které můžete nakonfigurovat. První z `interval`nich je , který definuje časový interval (v milisekundách) mezi zprávami odeslaných do cloudu. Druhý je `simulatedData`, což je logická hodnota pro to, zda použít simulovaná data senzoru nebo ne.

   Pokud **senzor nemáte**, nastavte hodnotu `simulatedData` `true` tak, aby ukázková aplikace vytvářela a používala simulovaná data senzorů.

   *Poznámka: Adresa i2c použitá v tomto kurzu je ve výchozím nastavení 0x77. V závislosti na konfiguraci může být také 0x76: pokud narazíte na chybu i2c, zkuste změnit hodnotu na 118 a zjistěte, zda to funguje lépe. Chcete-li zjistit, jakou adresu `sudo i2cdetect -y 1` používá váš senzor, spusťte ve skořápce na malinovém pi*

2. Uložit a ukončit zadáním Control-O > Zadejte > Control-X.

### <a name="run-the-sample-application"></a>Spuštění ukázkové aplikace

Spusťte ukázkovou aplikaci spuštěním následujícího příkazu:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Ujistěte se, že zkopírujete a vložíte připojovací řetězec zařízení do jednotlivých uvozovek.

Měl by se zobrazit následující výstup s daty snímače a zprávami posílanými do vašeho centra IoT Hub.

![Výstup – data snímače odesílaná z Raspberry Pi do centra IoT](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Čtení zpráv přijatých vaším centrem

Jedním ze způsobů, jak sledovat zprávy přijaté vaším centrem IoT hub z vašeho zařízení, je použití nástroje Azure IoT Tools for Visual Studio Code. Další informace najdete [v tématu Použití nástrojů Azure IoT pro kód Visual Studia k odesílání a přijímání zpráv mezi zařízením a službou IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Další způsoby zpracování dat odeslaných zařízením najdete v další části.

## <a name="next-steps"></a>Další kroky

Spouštěli jste ukázkovou aplikaci pro shromažďování dat senzorů a jejich odeslání do služby IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
