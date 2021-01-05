---
title: Připojení malinu pi k Azure IoT Hub v cloudu (Node.js)
description: Naučte se, jak nastavit a připojit maliny v/v Azure IoT Hub pro malinu pi k odesílání dat na cloudovou platformu Azure v tomto kurzu.
author: wesmc7777
manager: eliotgra
keywords: Azure IoT malin PI, malin PI IoT Hub, Malina Pi poslat data do cloudu, malin. PI do cloudu
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: c96f674b64401250d45542d0f59f13654cf37caa
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802520"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Připojit malinu pi k Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu začnete seznámení se základy práce s malinou PI, na kterých běží Raspbian. Pak se dozvíte, jak hladce připojit zařízení ke cloudu pomocí služby [Azure IoT Hub](about-iot-hub.md). Ukázky pro Windows 10 IoT Core najdete na webu [Windows Dev Center](https://www.windowsondevices.com/).

Ještě nemáte k dispozici sadu? Zkuste použít [simulátor malinu PI online](iot-hub-raspberry-pi-web-simulator-get-started.md). [Můžete](https://azure.microsoft.com/develop/iot/starter-kits)si také koupit novou sadu.

## <a name="what-you-do"></a>Co dělat

* Vytvořte centrum IoT.

* Zaregistrujte zařízení pro PI ve službě IoT Hub.

* Nastavte malinu (Malin) PI.

* Spusťte v PI ukázkovou aplikaci, která odešle data ze senzorů do služby IoT Hub.

## <a name="what-you-learn"></a>Co se naučíte

* Jak vytvořit službu Azure IoT Hub a získat nový připojovací řetězec zařízení.

* Jak připojit pi k BME280 senzoru.

* Jak shromažďovat data ze senzorů spuštěním ukázkové aplikace na pi.

* Jak odesílat data ze senzorů do služby IoT Hub.

## <a name="what-you-need"></a>Co budete potřebovat

![Co budete potřebovat](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Deska maliny pi 2 nebo malin PI 3.

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Monitor, klávesnice USB a myš, které se připojují k Pi.

* Počítač Mac nebo počítač se systémem Windows nebo Linux.

* Připojení k Internetu.

* Karta microSD o velikosti 16 GB nebo vyšší.

* Adaptér USB-SD nebo karta microSD, která zapisuje bitovou kopii operačního systému na kartu microSD.

* Zdroj napájení 5 Volt 2-amp s kabelem 6 – kabelem USB.

Následující položky jsou volitelné:

* Sestrojované teploty Adafruit BME280, tlak a snímač vlhkosti.

* Breadboard.

* 6 vodičů můstků F/M.

* Indikátor rozptýlený 10 mm.

> [!NOTE]
> Pokud nemáte volitelné položky, můžete použít Simulovaná data senzorů.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Nastavení malin. PI

### <a name="install-the-raspbian-operating-system-for-pi"></a>Nainstalujte operační systém Raspbian pro PI.

Připravte kartu microSD pro instalaci image Raspbian.

1. Stáhněte si Raspbian.

   a. [Raspbian Buster s desktopem](https://www.raspberrypi.org/software/) (soubor. zip).

   b. Rozbalte image Raspbian do složky v počítači.

2. Nainstalujte Raspbian na kartu microSD.

   a. [Stáhněte a nainstalujte si nástroj pro vypalovací kartu SD karty leptání](https://etcher.io/).

   b. Spusťte leptání a vyberte Image Raspbian, kterou jste extrahovali v kroku 1.

   c. Vyberte diskovou kartu microSD. Leptaný již pravděpodobně vybrala správnou jednotku.

   d. Kliknutím na flash nainstalujte Raspbian na kartu microSD.

   e. Až se instalace dokončí, odeberte kartu microSD z počítače. Kartu microSD je bezpečné odebrat přímo, protože leptání po dokončení automaticky vysune nebo odpojí kartu microSD.

   f. Vložte kartu microSD do Pi.

### <a name="enable-ssh-and-i2c"></a>Povolit SSH a I2C

1. Připojte pi k monitoru, klávesnici a myši.

2. Spusťte PI a pak se přihlaste k Raspbian pomocí `pi` uživatelského jména a `raspberry` hesla.

3. Klikněte na ikonu maliny > **Předvolby**  >  **malin. PI**.

   ![Nabídka předvolby Raspbian](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. Na kartě **rozhraní** nastavte **I2C** a **SSH** na **Povolit** a pak klikněte na **OK**. Pokud nemáte fyzické senzory a chcete použít Simulovaná data senzorů, je tento krok nepovinný.

   ![Povolit I2C a SSH v malinách PI](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Pokud chcete povolit SSH a I2C, můžete najít další referenční dokumenty v [RaspberryPI.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) a [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Připojit senzor k Pi

Použijte Breadboard a vodiče můstků k připojení indikátoru LED a BME280 k Pi následujícím způsobem. Pokud senzor nemáte, [přeskočte tuto část](#connect-pi-to-the-network).

![Připojení k malinu PI a snímač](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

Senzor BME280 může shromažďovat data o teplotě a vlhkosti. Indikátor LED bude blikat, když zařízení pošle zprávu do cloudu.

Pro PIN kódy senzorů použijte následující kabely:

| Start (indikátor & snímače)     | Konec (panel)            | Barva kabelu   |
| -----------------------  | ---------------------- | ------------: |
| VDD (připnout 5G)             | 3.3 v PWR (PIN kód 1)       | Bílý kabel   |
| GND (připnout 7G)             | GND (PIN 6)            | Kabel Brown   |
| SDI (PIN 10G)            | I2C1 SDA (PIN 3)       | Červený kabel     |
| SCK (připnout 8G)             | I2C1 SQL (PIN 5)       | Oranžový kabel  |
| VDD LED (připnout 18F)        | GPIO 24 (PIN 18)       | Bílý kabel   |
| GND LED (připnout 17F)        | GND (PIN 20)           | Černý kabel   |

Kliknutím zobrazíte [& 3 – mapování kódu PIN pro malin 2](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) pro váš odkaz.

Po úspěšném připojení BME280 k malinu PI by měl být podobný jako obrázek.

![Připojené PI a BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Připojit pi k síti

Zapněte Pi pomocí kabelu micro USB a zdroje napájení. Pomocí kabelu Ethernet připojte pi k pevné síti nebo podle [pokynů od Malin. PI Foundation](https://www.raspberrypi.org/documentation/configuration/wireless/) připojte pi k bezdrátové síti. Až se vaše PI úspěšně připojí k síti, musíte si poznamenat [IP adresu vašeho PI](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Připojeno k drátové síti](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Ujistěte se, že je pi připojen ke stejné síti jako váš počítač. Například pokud je počítač připojený k bezdrátové síti, zatímco je pi připojená k drátové síti, nemusí se tato IP adresa zobrazit ve výstupu devdisco.

## <a name="run-a-sample-application-on-pi"></a>Spustit ukázkovou aplikaci na PI

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Klonovat ukázkovou aplikaci a nainstalovat požadované balíčky

1. Připojte se k malin. PI jedním z následujících klientů SSH z hostitelského počítače:

   **Uživatelé systému Windows**

   a. Stažení a instalace [výstupu](https://www.putty.org/) pro Windows

   b. Zkopírujte IP adresu vašeho PI do části název hostitele (nebo IP adresa) a jako typ připojení vyberte SSH.

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Uživatelé počítačů Mac a Ubuntu**

   Použijte integrovaného klienta SSH na Ubuntu nebo macOS. Možná budete muset spustit `ssh pi@<ip address of pi>` pro připojení Pi přes SSH.

   > [!NOTE]
   > Výchozí uživatelské jméno je `pi` a heslo `raspberry` .

2. Nainstalujte Node.js a NPM do svého PI.

   Nejdřív ověřte Node.js verzi.

   ```bash
   node -v
   ```

   Pokud je verze nižší než 10. x nebo pokud na pi není Node.js, nainstalujte nejnovější verzi.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Naklonujte ukázkovou aplikaci.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Nainstalujte všechny balíčky pro ukázku. Instalace zahrnuje sadu SDK pro zařízení Azure IoT, knihovnu senzorů BME280 a knihovnu pro kabeláž PI.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >Dokončení tohoto procesu instalace může trvat několik minut v závislosti na připojení k síti.

### <a name="configure-the-sample-application"></a>Konfigurace ukázkové aplikace

1. Otevřete konfigurační soubor spuštěním následujících příkazů:

   ```bash
   nano config.json
   ```

   ![Konfigurační soubor](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   V tomto souboru jsou dvě položky, které můžete nakonfigurovat. První z nich je `interval` , který definuje časový interval (v milisekundách) mezi zprávami odesílanými do cloudu. Druhá je `simulatedData` , což je logická hodnota, která určuje, jestli se mají použít Simulovaná data senzoru nebo ne.

   Pokud nemáte **senzor**, nastavte hodnotu na, aby `simulatedData` `true` ukázková aplikace vytvořila a používala Simulovaná data snímače.

   *Poznámka: adresa I2C použitá v tomto kurzu je standardně 0x77. V závislosti na vaší konfiguraci může být také 0x76: Pokud narazíte na chybu I2C, zkuste změnit hodnotu na 118 a zjistit, jestli to funguje lépe. Pokud chcete zjistit, jakou adresu používá váš senzor, spusťte `sudo i2cdetect -y 1` v prostředí na Maline PI.*

2. Uložte a ukončete zadáním příkazu Control-O > zadejte > Control-X.

### <a name="run-the-sample-application"></a>Spuštění ukázkové aplikace

Spusťte ukázkovou aplikaci spuštěním následujícího příkazu:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Nezapomeňte zkopírovat a vložit připojovací řetězec zařízení do jednoduchých uvozovek.

Měl by se zobrazit následující výstup s daty snímače a zprávami posílanými do vašeho centra IoT Hub.

![Výstup – data snímače odesílaná z Raspberry Pi do centra IoT](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Přečtěte si zprávy přijaté vaším rozbočovačem.

Jedním ze způsobů, jak monitorovat zprávy přijaté službou IoT Hub ze svého zařízení, je používat nástroje Azure IoT pro Visual Studio Code. Další informace najdete v tématu [použití nástrojů Azure IoT pro Visual Studio Code k posílání a přijímání zpráv mezi zařízením a IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Další způsoby, jak zpracovávat data odesílaná vaším zařízením, můžete pokračovat k další části.

## <a name="next-steps"></a>Další kroky

Spustili jste ukázkovou aplikaci, která shromáždí data ze senzorů a pošle ji do služby IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
