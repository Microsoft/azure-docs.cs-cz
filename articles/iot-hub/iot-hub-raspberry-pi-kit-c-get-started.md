---
title: Malinová pí do cloudu (C) - pí malin připojit ke službě Azure IoT Hub | Microsoft Docs
description: Zjistěte, jak nastavit a připojení k Azure IoT Hub malin pí k odesílání dat do Azure Cloudová platforma v tomto kurzu malin pí.
author: rangv
manager: ''
keywords: Azure iot Malinová pi, malinová platformy iot hub, malinová pí odesílání dat do cloudu, malinová pí do cloudu
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 5501bc0eb65737bc779d0624195a4ecdf670eafd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635657"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Připojení k Azure IoT Hub (C) Malinová platformy

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu zahájíte učení základní informace o práci s malin platformy, na kterém běží Raspbian. Pak zjistíte, jak bezproblémově připojení zařízení do cloudu pomocí [Azure IoT Hub](iot-hub-what-is-iot-hub.md). Ukázky jádro IoT Windows 10, najdete [Centrum vývojářů pro Windows](http://www.windowsondevices.com/).

Nemáte sady ještě? Zkuste [online simulátoru malin pí](iot-hub-raspberry-pi-web-simulator-get-started.md). Nebo zakoupení nové kit [zde](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Co dělat

* Vytvoření služby IoT hub.
* Registrovat zařízení pro platformy ve službě IoT hub.
* Instalační program Malinová pí.
* Spuštění ukázkové aplikace na platformy k odesílání dat snímačů do služby IoT hub.

Pi malin připojení do služby IoT hub, který vytvoříte. Pak spusťte ukázkovou aplikaci na platformy ke shromažďování dat teploty a vlhkosti ze BME280 senzoru. Nakonec odeslat data snímačů do služby IoT hub.

## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby Azure IoT hub a získat nový připojovací řetězec zařízení.
* Postup připojení pí s BME280 senzoru.
* Postup shromažďování dat snímačů spuštěním ukázkovou aplikaci na pí.
* Jak odesílat data snímačů do služby IoT hub.

## <a name="what-you-need"></a>Co potřebujete

![Co potřebujete](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Malin pí 2 nebo 3 pí malin panelu.
* Aktivní předplatné Azure. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Monitorování, USB klávesnice a myši připojujících se k pí.
* Mac nebo počítači se systémem Windows nebo Linux.
* Připojení k Internetu.
* 16 GB nebo vyšší microSD karta.
* USB-adaptér nebo microSD karta SD vypálíte image operačního systému na kartě microSD.
* 5 volt 2 amp napájení s 6 stopy malých kabel USB.

Následující položky jsou volitelné:

* Sestavený Adafruit BME280 teploty, naléhavost a vlhkosti senzoru.
* Breadboard.
* Vodičům můstek 6 F/M.
* Rozptýlený DIODU 10 mm.


> [!NOTE] 
Tyto položky jsou volitelné, protože data snímačů simulated podporu ukázkový kód.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Instalační program Malinová platformy

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalace operačního systému Raspbian pí

Připravte karty microSD pro instalaci bitové kopie Raspbian.

1. Stáhněte si Raspbian.
   1. [Stáhnout Raspbian Klára plochy](https://www.raspberrypi.org/downloads/raspbian/) (soubor .zip).
   1. Extrahujte Raspbian image do složky v počítači.
1. Nainstalujte Raspbian microSD karta.
   1. [Stáhněte a nainstalujte nástroj Etcher SD karty zapisovací jednotka](https://etcher.io/).
   1. Spusťte Etcher a vyberte Raspbian bitovou kopii, která jste extrahovali v kroku 1.
   1. Vyberte jednotku microSD karta. Všimněte si, že Etcher může jste již vybrali správnou jednotku.
   1. Klikněte na tlačítko nainstalovat Raspbian do karty microSD Flash.
   1. Karta microSD odeberte z počítače, po dokončení instalace. Je bezpečné karty microSD přímo odebrat, protože Etcher automaticky vysune nebo odpojí microSD karta po dokončení.
   1. Karta microSD vložte do pí.

### <a name="enable-ssh-and-spi"></a>Povolit SSH a SPI

1. Připojit k monitoru, klávesnice a myši platformy, spusťte platformy a znovu přihlásili Raspbian pomocí `pi` jako uživatelské jméno a `raspberry` jako heslo.
1. Klikněte na ikonu Malinová > **Předvolby** > **malin pí konfigurace**.

   ![V nabídce Raspbian předvolby](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Na **rozhraní** nastavte **SPI** a **SSH** k **povolit**a pak klikněte na **OK**. Pokud nemáte fyzické senzory a chcete použít data simulované snímačů, tento krok je volitelný.

   ![Povolit SPI a SSH na Malinová platformy](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
SSH a SPI povolit, můžete najít další dokumenty odkaz na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) a [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Připojit senzoru PI

Pomocí vedení breadboard a můstek pro připojení DIODU a BME280 PI následujícím způsobem. Pokud nemáte senzoru, [tuto část přeskočte](#connect-pi-to-the-network).

![Připojení malin platformy a senzor](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

BME280 senzoru teploty a vlhkosti data můžete shromažďovat. A DIODU bude blink – Pokud je komunikace mezi zařízením a cloudem. 

Senzor kód PIN použijte následující kabeláž:

| Spuštění (senzor & DIODU)     | End (panelu)            | Kabel barev   |
| -----------------------  | ---------------------- | ------------: |
| Indikátor VDD (Pin 5G)         | GPIO 4 (Pin 7)         | Bílé kabel   |
| Indikátor zem (Pin 6G)         | ZEM (Pin 6)            | Začernit kabel   |
| VDD (Pin 18F)            | 3.3v opravná (Pin 17)      | Bílé kabel   |
| ZEM (Pin 20F)            | ZEM (Pin 20)           | Začernit kabel   |
| SCK (Pin 21F)            | SPI0 SCLK (Pin 23)     | Oranžové kabel  |
| SDO (Pin 22F)            | SPI0 MISO (Pin 21)     | Žlutý kabel  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin 19)     | Zelená kabel   |
| CS (Pin 24F)             | SPI0 CS (Pin 24)       | Modrý kabel    |

Kliknutím zobrazíte [malin pí 2 a 3 mapování kódu Pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) pro vaši informaci.

Po připojení BME280 úspěšně vaší malin PI, mělo by být jako níže bitové kopie.

![Připojené platformy a BME280](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Připojení k síti platformy

Zapněte pí pomocí kabelu USB micro a napájení. Pomocí kabelu Ethernet připojit platformy k drátové síti nebo postupujte podle [pokyny z Foundation pí malin](https://www.raspberrypi.org/learning/software-guide/wifi/) pro připojení k bezdrátové síti pí. Po vaší platformy se úspěšně připojil k síti, budete muset poznamenejte [IP adresa vašeho čísla pí](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Připojení k drátové síti](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Spuštění ukázkové aplikace na platformy

### <a name="login-to-your-raspberry-pi"></a>Přihlášení k vaší Malinová platformy

1. Použijte jeden z následujících klientů SSH z hostitelského počítače pro připojení k vaší malin platformy.
   
   **Uživatelé s Windows**
   1. Stáhněte a nainstalujte [PuTTY](http://www.putty.org/) pro systém Windows. 
   1. Zkopírujte IP adresu vašeho pí do název hostitele (nebo IP adresu) oddílu a vyberte jako typ připojení SSH.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac a Ubuntu uživatelů**
   
   Použijte integrovaného klienta SSH na Ubuntu nebo systému macOS. Možná budete muset spustit `ssh pi@<ip address of pi>` připojit platformy prostřednictvím SSH.
   > [!NOTE] 
   Výchozí uživatelské jméno `pi` , a heslo je `raspberry`.


### <a name="configure-the-sample-application"></a>Nakonfigurujte ukázkovou aplikaci

1. Naklonujte ukázkovou aplikaci tak, že spustíte následující příkaz:

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
   > Pokud jste **nemají fyzické BME280**, můžete použít ' – simulated-data "jako parametr příkazového řádku k simulaci teploty a vlhkosti data. `sudo ./setup.sh --simulated-data`

### <a name="build-and-run-the-sample-application"></a>Sestavení a spuštění ukázkové aplikace

1. Vytvoření ukázkové aplikace tak, že spustíte následující příkaz:

   ```bash
   cmake . && make
   ```
   ![Sestavení výstupu](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Spuštění ukázkové aplikace tak, že spustíte následující příkaz:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Zkontrolujte, zda jste způsobené kopírováním a vkládáním zařízení připojovací řetězec do jednoduchých uvozovek.


Měli byste vidět následující výstup, který popisuje data snímačů a zprávy, které se odesílají do služby IoT hub.

![Výstup – data snímačů odeslaný malin pí do služby IoT hub](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Další postup

Spustíte ukázkovou aplikaci pro shromažďování dat snímačů a odeslat do služby IoT hub. Zprávy, které vaše platformy malin odeslal na IoT hub nebo odesílání zprávy pro vaše platformy malin v rozhraní příkazového řádku najdete v sekci [cloud zařízení spravovat zasílání zpráv s iothub-explorer kurzu](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
