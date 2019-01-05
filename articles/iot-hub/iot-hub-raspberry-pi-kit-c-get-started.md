---
title: Raspberry Pi do cloudu (C) – připojte Raspberry Pi pro službu Azure IoT Hub | Dokumentace Microsoftu
description: Zjistěte, jak nastavit a Raspberry Pi připojit ke službě Azure IoT Hub pro Raspberry Pi k odesílání dat do cloudové platformy Azure v tomto kurzu.
author: rangv
manager: ''
keywords: Azure iot raspberry pi, raspberry pi iot hub, raspberry pi odesílání dat do cloudu, raspberry pi s cloudem
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: c8b254656dcd94fd846336e9c9de3ca823691904
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053496"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Připojení k Azure IoT Hub (C) Raspberry Pi

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu začnete pomocí seznámení se základy práce s Raspberry Pi, na kterém běží Raspbian. Pak se naučíte bez problémů propojit si zařízení do cloudu s využitím [Azure IoT Hub](about-iot-hub.md). Ukázky Windows 10 IoT Core najdete [Windows Dev Center](http://www.windowsondevices.com/).

Sadu ještě nemáte? Zkuste [online simulátor Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Nebo zakoupit sadu nových [tady](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Co můžete dělat

* Vytvoření služby IoT hub.
* Registrace zařízení ve službě IoT hub pro číslo pí.
* Instalační program Raspberry Pi.
* Spusťte ukázkovou aplikaci v Pi odeslat data ze senzorů do služby IoT hub.

Připojte Raspberry Pi do služby IoT hub, kterou vytvoříte. Pak spusťte ukázkovou aplikaci v Pi shromažďovat údaje o teplotě a vlhkosti BME280 senzoru. A konečně odeslat data ze senzorů do služby IoT hub.

## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby Azure IoT hub a získat nový připojovací řetězec zařízení.
* Jak se připojit Pi s BME280 senzoru.
* Jak shromažďovat data ze senzorů spuštěním ukázkové aplikace na číslo pí.
* Jak odeslat data ze senzorů do služby IoT hub.

## <a name="what-you-need"></a>Co potřebujete

![Co potřebujete](./media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Raspberry Pi 2 nebo Raspberry Pi 3 panelu.
* Aktivní předplatné Azure. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Monitorování, USB klávesnice a myši, která se připojují k pí.
* Na Macu nebo počítači se systémem Windows nebo Linux.
* Připojení k Internetu.
* 16 GB nebo nad microSD karta.
* USB SD adaptér nebo microSD karta pro vypalování image operačního systému na kartě microSD.
* Mocninou čísla 2 a 5 volt zadat pomocí USB kabelu micro 6 stopy.

Následující položky jsou volitelné:

* Deskách Adafruit BME280 teploty, tlaku a vlhkosti senzoru.
* Breadboard.
* 6 F/milion můstek vodičům stanice.
* Rozptýlený LED 10 mm.

> [!NOTE] 
> Tyto položky jsou volitelné, protože podpora vzorek kódu Simulovaná data ze senzorů.
>

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro službu IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zaregistrujte nové zařízení ve službě IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="setup-raspberry-pi"></a>Nastavení Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalace operačního systému Raspbian Pi

Příprava pro instalaci bitové kopie Raspbian karty microSD.

1. Stáhněte si Raspbian.
   1. [Stáhnout Raspbian Jessie s Desktopem](https://www.raspberrypi.org/downloads/raspbian/) (soubor .zip).
   1. Extrahujte Raspbian image do složky v počítači.
1. Instalace na kartě microSD Raspbian.
   1. [Stáhněte a nainstalujte nástroj zapisovací jednotka karty Etcher SD](https://etcher.io/).
   1. Spusťte Etcher a vyberte Raspbian bitové kopie, které jste extrahovali v kroku 1.
   1. Vyberte jednotku microSD karta. Všimněte si, že Etcher možná jste již vybrali správná jednotka.
   1. Klepněte na Flash instalace Raspbian microSD karta.
   1. Karta microSD neodeberete z počítače, po dokončení instalace. Jde bezpečně odebrat microSD karta přímo, protože Etcher automaticky vysune nebo odpojí microSD karta po dokončení.
   1. Vložte karty microSD do pí.

### <a name="enable-ssh-and-spi"></a>Povolit SSH a SPI

1. Připojit k monitoru, klávesnice a myši Pi, spusťte Pi a přihlaste Raspbian pomocí `pi` jako uživatelské jméno a `raspberry` jako heslo.
1. Klikněte na ikonu Raspberry > **Předvolby** > **Raspberry Pi konfigurace**.

   ![V nabídce Předvolby Raspbian](./media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Na **rozhraní** kartu, nastavte **SPI** a **SSH** k **povolit**a potom klikněte na tlačítko **OK**. Pokud nemáte fyzické senzory a chcete použít data ze simulovaných senzorů, tento krok je volitelný.

   ![Povolit SPI a SSH v Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
> Pokud chcete povolit SSH a SPI, najdete další referenční dokumenty na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) a [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Připojit senzor PI

Breadboard a můstek vodičům stanice slouží k připojení kontrolku LED a BME280 PI následujícím způsobem. Pokud nemáte senzor [tuto část přeskočit](#connect-pi-to-the-network).

![Připojení Raspberry Pi a snímače](./media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

Snímač BME280 může shromažďovat data o teplotě a vlhkosti. A bude indikátor LED blikání při komunikaci mezi zařízením a cloudem. 

Senzor kód PIN použijte následující propojení:

| Spustit (senzor & Indikátor)     | End (panel)            | Barva kabel   |
| -----------------------  | ---------------------- | ------------: |
| Indikátor VDD (Pin 5G)         | GPIO 4 (Pin 7)         | Bílé kabel   |
| Indikátor zem (Pin 6G)         | ZEM (Pin 6)            | Černého kabelu pak   |
| VDD (Pin 18F)            | 3,3 opravná (Pin 17)      | Bílé kabel   |
| ZEM (Pin 20F)            | ZEM (Pin 20)           | Černého kabelu pak   |
| SCK (Pin 21F)            | SPI0 SCLK (Pin 23)     | Oranžové kabel  |
| SDO (Pin 22F)            | SPI0 MISO (Pin 21)     | Žlutý kabel  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin 19)     | Zelená kabel   |
| CS (Pin 24F)             | SPI0 CS (Pin 24)       | Modrý kabel    |

Kliknutím zobrazíte [Raspberry Pi 2 a 3 mapování kódu Pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) pro vaši informaci.

Poté, co právě jste úspěšně propojili BME280 Raspberry Pi, měla by být jako obrázku níže.

![Připojené Pi a BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Připojit k síti, Pi

Zapněte pí pomocí USB kabelu micro a napájení. Pomocí kabelu Ethernet Pi připojení k drátové síti ani postupovat podle pokynů [pokyny od Raspberry Pi základ](https://www.raspberrypi.org/learning/software-guide/wifi/) Pi připojit k bezdrátové síti. Až váš Pi byl úspěšně připojen k síti, budete muset poznamenejte si [IP adresu vašeho pí](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Připojený k drátové síti](./media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Spustit ukázkovou aplikaci v Pi

### <a name="login-to-your-raspberry-pi"></a>Přihlaste se k Raspberry Pi

1. Použijte jednu z následujících klientů SSH z hostitelského počítače pro připojení k Raspberry Pi.
   
   **Uživatelé Windows**
   1. Stáhněte a nainstalujte [PuTTY](http://www.putty.org/) pro Windows. 
   1. Zkopírujte IP adresu části Pi do názvu hostitele (nebo IP adresu) a vyberte jako typ připojení SSH.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac a uživatelé Ubuntu**
   
   Pomocí integrovaného klienta SSH v systému Ubuntu nebo macOS. Možná budete muset spustit `ssh pi@<ip address of pi>` připojit Pi přes protokol SSH.
   > [!NOTE] 
   Výchozí uživatelské jméno `pi` , a heslo je `raspberry`.


### <a name="configure-the-sample-application"></a>Nakonfigurovat ukázkovou aplikaci

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
   > Pokud jste **nemají fyzické BME280**, můžete použít "--Simulovaná data' jako parametr příkazového řádku pro simulaci teploty a vlhkosti data. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Sestavení a spuštění ukázkové aplikace

1. Vytvoření ukázkové aplikace spuštěním následujícího příkazu:

   ```bash
   cmake . && make
   ```
   
   ![Výstup sestavení](./media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Spuštění ukázkové aplikace spuštěním následujícího příkazu:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Ujistěte se, že je kopírování a vkládání připojovací řetězec zařízení v jednoduchých uvozovkách.
   >

Měli byste vidět následující výstup, který zobrazuje data ze senzorů a zprávy, které se odesílají do služby IoT hub.

![Výstup – data ze senzorů odeslané do služby IoT hub z Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Další postup

Jste spustili ukázkovou aplikaci shromažďovat data ze senzorů a odesílat je do služby IoT hub. Zprávy, které služba IoT hub nebo odesílání zpráv do Raspberry Pi odeslal Raspberry Pi najdete v tématu [pomocí Azure IoT, nástrojů pro Visual Studio Code k odesílání a příjem zpráv mezi zařízením a centrem IoT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
