---
title: Připojení malinu pi k Azure IoT Hub pomocí jazyka C | Microsoft Docs
description: Naučte se, jak nastavit a připojit malinu PI do Azure IoT Hub pro malinu pi k odesílání dat na cloudovou platformu Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 52da3c28faa375f8b308e3fe78329fec4f996af9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97804050"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Připojit malinu pi k Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu začnete seznámení se základy práce s malinou PI, na kterých běží Raspbian. Pak se dozvíte, jak hladce připojit zařízení ke cloudu pomocí služby [Azure IoT Hub](about-iot-hub.md). Ukázky pro Windows 10 IoT Core najdete na webu [Windows Dev Center](https://www.windowsondevices.com/).

Ještě nemáte k dispozici sadu? Zkuste použít [simulátor malinu PI online](iot-hub-raspberry-pi-web-simulator-get-started.md). [Můžete](https://azure.microsoft.com/develop/iot/starter-kits)si také koupit novou sadu.

## <a name="what-you-do"></a>Co dělat

* Vytvořte centrum IoT.

* Zaregistrujte zařízení pro PI ve službě IoT Hub.

* Instalační program maliny PI.

* Spusťte v PI ukázkovou aplikaci, která odešle data ze senzorů do služby IoT Hub.

Připojte malinu PI ke službě IoT Hub, kterou vytvoříte. Pak spustíte ukázkovou aplikaci na PI a shromáždíte data o teplotě a vlhkosti ze senzoru BME280. Nakonec odešlete data ze senzorů do služby IoT Hub.

## <a name="what-you-learn"></a>Co se naučíte

* Jak vytvořit službu Azure IoT Hub a získat nový připojovací řetězec zařízení.

* Jak připojit pi k BME280 senzoru.

* Jak shromažďovat data ze senzorů spuštěním ukázkové aplikace na pi.

* Jak odesílat data ze senzorů do služby IoT Hub.

## <a name="what-you-need"></a>Co budete potřebovat

![Co budete potřebovat](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Deska Malina Pi 2 nebo malin PI 3.

* Musíte mít aktivní předplatné Azure. Pokud účet Azure nemáte, [Vytvořte si bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) během několika minut.

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
> Tyto položky jsou volitelné, protože ukázka kódu podporuje Simulovaná data senzorů.
>

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Nastavení malin. PI

Teď vytvořte soubor malin. PI.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Nainstalujte operační systém Raspbian pro PI.

Připravte kartu microSD pro instalaci image Raspbian.

1. Stáhněte si Raspbian.

   1. [Stáhněte si Raspbian Stretch with Desktop](https://www.raspberrypi.org/software/) (soubor. zip).

   2. Rozbalte image Raspbian do složky v počítači.

2. Nainstalujte Raspbian na kartu microSD.

   1. [Stáhněte a nainstalujte si nástroj pro vypalovací kartu SD karty leptání](https://etcher.io/).

   2. Spusťte leptání a vyberte Image Raspbian, kterou jste extrahovali v kroku 1.

   3. Vyberte diskovou kartu microSD. Všimněte si, že leptaný už možná vybrala správnou jednotku.

   4. Kliknutím na flash nainstalujte Raspbian na kartu microSD.

   5. Až se instalace dokončí, odeberte kartu microSD z počítače. Kartu microSD je bezpečné odebrat přímo, protože leptání po dokončení automaticky vysune nebo odpojí kartu microSD.

   6. Vložte kartu microSD do Pi.

### <a name="enable-ssh-and-spi"></a>Povolit SSH a SPI

1. Připojte pi k monitoru, klávesnici a myši, spusťte PI a pak se přihlaste k Raspbian pomocí `pi` uživatelského jména a `raspberry` hesla.
 
2. Klikněte na ikonu maliny > **Předvolby**  >  **malin. PI**.

   ![Nabídka předvolby Raspbian](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Na kartě **rozhraní** nastavte **SPI** a **SSH** na **Povolit** a pak klikněte na **OK**. Pokud nemáte fyzické senzory a chcete použít Simulovaná data senzorů, je tento krok nepovinný.

   ![Povolit SPI a SSH v malinách PI](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Pokud chcete povolit SSH a SPI, můžete najít další referenční dokumenty v [RaspberryPI.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) a [RASPI-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Připojit senzor k Pi

Použijte Breadboard a vodiče můstků k připojení indikátoru LED a BME280 k Pi následujícím způsobem. Pokud senzor nemáte, [přeskočte tuto část](#connect-pi-to-the-network).

![Připojení k malinu PI a snímač](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

Senzor BME280 může shromažďovat data o teplotě a vlhkosti. A indikátor LED bude blikat, pokud dojde ke komunikaci mezi zařízením a cloudem.

Pro PIN kódy senzorů použijte následující kabely:

| Start (indikátor & snímače)     | Konec (panel)            | Barva kabelu   |
| -----------------------  | ---------------------- | ------------: |
| VDD LED (připnout 5G)         | GPIO 4 (PIN 7)         | Bílý kabel   |
| GND LED (připnout 6G)         | GND (PIN 6)            | Černý kabel   |
| VDD (připnout 18F)            | 3.3 v PWR (PIN 17)      | Bílý kabel   |
| GND (připnout 20F)            | GND (PIN 20)           | Černý kabel   |
| SCK (připnout 21F)            | SPI0 SCLK (PIN kód 23)     | Oranžový kabel  |
| SDO (PIN 22F)            | SPI0 MISO (PIN 21)     | Žlutý kabel  |
| SDI (PIN 23F)            | SPI0 MOSI (PIN 19)     | Zelený kabel   |
| CS (PIN 24F)             | SPI0 CS (PIN kód 24)       | Modrý kabel    |

Kliknutím zobrazíte [& 3 – mapování kódu PIN pro malin 2](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) pro váš odkaz.

Po úspěšném připojení BME280 k malinu PI by měl být podobný jako obrázek.

![Připojené PI a BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Připojit pi k síti

Zapněte Pi pomocí kabelu micro USB a zdroje napájení. Pomocí kabelu Ethernet připojte pi k pevné síti nebo podle [pokynů od Malin. PI Foundation](https://www.raspberrypi.org/documentation/configuration/wireless/) připojte pi k bezdrátové síti. Až se vaše PI úspěšně připojí k síti, musíte si poznamenat [IP adresu vašeho PI](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Připojeno k drátové síti](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Spustit ukázkovou aplikaci na PI

### <a name="sign-into-your-raspberry-pi"></a>Přihlaste se k malinu PI.

1. Pomocí jednoho z následujících klientů SSH z hostitelského počítače se připojte k malinu PI.
   
   **Uživatelé systému Windows**
   1. Stažení a instalace [výstupu](https://www.putty.org/) pro Windows 
   1. Zkopírujte IP adresu vašeho PI do části název hostitele (nebo IP adresa) a jako typ připojení vyberte SSH.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Uživatelé počítačů Mac a Ubuntu**

   Použijte integrovaného klienta SSH na Ubuntu nebo macOS. Možná budete muset spustit `ssh pi@<ip address of pi>` pro připojení Pi přes SSH.
   > [!NOTE]
   > Výchozí uživatelské jméno je `pi` a heslo `raspberry` .


### <a name="configure-the-sample-application"></a>Konfigurace ukázkové aplikace

1. Naklonujte ukázkovou aplikaci spuštěním následujícího příkazu:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Spusťte instalační skript:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Pokud nemáte **fyzický BME280**, můžete k simulaci teploty&dat vlhkosti použít "--Simulovaná data" jako parametr příkazového řádku. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Sestavení a spuštění ukázkové aplikace

1. Sestavte ukázkovou aplikaci spuštěním následujícího příkazu:

   ```bash
   cmake . && make
   ```
   
   ![Výstup sestavení](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Spusťte ukázkovou aplikaci spuštěním následujícího příkazu:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Nezapomeňte zkopírovat a vložit připojovací řetězec zařízení do jednoduchých uvozovek.
   >

Měl by se zobrazit následující výstup s daty snímače a zprávami posílanými do vašeho centra IoT Hub.

![Výstup – data snímače odesílaná z Raspberry Pi do centra IoT](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Přečtěte si zprávy přijaté vaším rozbočovačem.

Jedním ze způsobů, jak monitorovat zprávy přijaté službou IoT Hub ze svého zařízení, je používat nástroje Azure IoT pro Visual Studio Code. Další informace najdete v tématu [použití nástrojů Azure IoT pro Visual Studio Code k posílání a přijímání zpráv mezi zařízením a IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Další způsoby, jak zpracovávat data odesílaná vaším zařízením, můžete pokračovat k další části.

## <a name="next-steps"></a>Další kroky

Spustili jste ukázkovou aplikaci, která shromáždí data ze senzorů a pošle ji do služby IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]