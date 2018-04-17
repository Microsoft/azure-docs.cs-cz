---
title: Malinová pí do cloudu (Node.js) - pí malin připojit ke službě Azure IoT Hub | Microsoft Docs
description: Zjistěte, jak nastavit a připojení k Azure IoT Hub malin pí k odesílání dat do Azure Cloudová platforma v tomto kurzu malin pí.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: Azure iot Malinová pi, malinová platformy iot hub, malinová pí odesílání dat do cloudu, malinová pí do cloudu
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e7db2f78a5c1a942f64a2c0a40068fffe90749d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Připojení ke službě Azure IoT Hub (Node.js) Malinová platformy

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu zahájíte učení základní informace o práci s malin platformy, na kterém běží Raspbian. Pak zjistíte, jak bezproblémově připojení zařízení do cloudu pomocí [Azure IoT Hub](iot-hub-what-is-iot-hub.md). Ukázky jádro IoT Windows 10, najdete [Centrum vývojářů pro Windows](http://www.windowsondevices.com/).

Nemáte sady ještě? Zkuste [online simulátoru malin pí](iot-hub-raspberry-pi-web-simulator-get-started.md). Nebo zakoupení nové kit [zde](https://azure.microsoft.com/develop/iot/starter-kits).


## <a name="what-you-do"></a>Co dělat

* Vytvoření služby IoT hub.
* Registrovat zařízení pro platformy ve službě IoT hub.
* Nastavte malin pí.
* Spuštění ukázkové aplikace na platformy k odesílání dat snímačů do služby IoT hub.

## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby Azure IoT hub a získat nový připojovací řetězec zařízení.
* Postup připojení pí s BME280 senzoru.
* Postup shromažďování dat snímačů spuštěním ukázkovou aplikaci na pí.
* Jak odesílat data snímačů do služby IoT hub.

## <a name="what-you-need"></a>Co potřebujete

![Co potřebujete](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* Malin pí 2 nebo 3 pí malin panelu.
* Aktivní předplatné Azure. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Monitorování, USB klávesnici a myš, která se připojuje k pí.
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
Pokud nemáte nepovinných položek, můžete data simulované senzoru.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Nastavit malin platformy

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalace operačního systému Raspbian pí

Připravte karty microSD pro instalaci bitové kopie Raspbian.

1. Stáhněte si Raspbian.
   1. [Stáhněte si Raspbian Stretch](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (soubor .zip).

   > [!WARNING]
   > Použijte výše odkaz ke stažení `raspbian-2017-07-5` obrázek zip. Nejnovější verzi Raspbian Image má některé známé problémy s uzlem kabeláž platformy, což by mohlo způsobit selhání v dalších krocích.
   1. Extrahujte Raspbian image do složky v počítači.

1. Nainstalujte Raspbian microSD karta.
   1. [Stáhněte a nainstalujte nástroj Etcher SD karty zapisovací jednotka](https://etcher.io/).
   1. Spusťte Etcher a vyberte Raspbian bitovou kopii, která jste extrahovali v kroku 1.
   1. Vyberte jednotku microSD karta. Etcher možná jste již vybrali správnou jednotku.
   1. Klikněte na tlačítko nainstalovat Raspbian do karty microSD Flash.
   1. Karta microSD odeberte z počítače, po dokončení instalace. Je bezpečné karty microSD přímo odebrat, protože Etcher automaticky vysune nebo odpojí microSD karta po dokončení.
   1. Karta microSD vložte do pí.

### <a name="enable-ssh-and-i2c"></a>Povolit SSH a I2C

1. Pi se připojte k monitoru, klávesnice a myši. 
1. Spuštění instalace platformy a znovu přihlásili Raspbian pomocí `pi` jako uživatelské jméno a `raspberry` jako heslo.
1. Klikněte na ikonu Malinová > **Předvolby** > **malin pí konfigurace**.

   ![V nabídce Raspbian předvolby](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

1. Na **rozhraní** nastavte **I2C** a **SSH** k **povolit**a pak klikněte na **OK**. Pokud nemáte fyzické senzory a chcete použít data simulované snímačů, tento krok je volitelný.

   ![Povolit I2C a SSH na Malinová platformy](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
SSH a I2C povolit, můžete najít další dokumenty odkaz na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) a [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Připojit senzoru PI

Pomocí vedení breadboard a můstek pro připojení DIODU a BME280 PI následujícím způsobem. Pokud nemáte senzoru, [tuto část přeskočte](#connect-pi-to-the-network).

![Připojení malin platformy a senzor](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

BME280 senzoru teploty a vlhkosti data můžete shromažďovat. Indikátor LED bliká, pokud je zařízení odesílá zprávy do cloudu. 

Senzor kód PIN použijte následující kabeláž:

| Spuštění (senzor & DIODU)     | End (panelu)            | Kabel barev   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5G)             | 3.3v opravná (Pin 1)       | Bílé kabel   |
| ZEM (Pin 7G)             | ZEM (Pin 6)            | Hnědá kabel   |
| SDI (Pin 10G)            | I2C1 SDA (PIN kódu 3)       | Red kabel     |
| SCK (Pin 8G)             | I2C1 SCL (Pin 5)       | Oranžové kabel  |
| Indikátor VDD (Pin 18F)        | GPIO 24 (Pin 18)       | Bílé kabel   |
| Indikátor zem (Pin 17F)        | ZEM (Pin 20)           | Začernit kabel   |

Kliknutím zobrazíte [malin pí 2 a 3 mapování kódu pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) pro vaši informaci.

Po připojení BME280 úspěšně vaší malin PI, mělo by být jako níže bitové kopie.

![Připojené platformy a BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Připojení k síti platformy

Zapněte pí pomocí kabelu USB micro a napájení. Pomocí kabelu Ethernet připojit platformy k drátové síti nebo postupujte podle [pokyny z Foundation pí malin](https://www.raspberrypi.org/learning/software-guide/wifi/) pro připojení k bezdrátové síti pí. Po vaší platformy se úspěšně připojil k síti, budete muset poznamenejte [IP adresa vašeho čísla pí](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Připojení k drátové síti](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Ujistěte se, že platformy je připojený ke stejné síti jako počítače. Například pokud je počítač připojen k bezdrátové síti a platformy je připojeno k drátové síti, nemusíte to vidět IP adresu ve výstupu devdisco.

## <a name="run-a-sample-application-on-pi"></a>Spuštění ukázkové aplikace na platformy

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Naklonujte ukázkovou aplikaci a nainstalujte požadované balíčky

1. Připojení k vaší malin pí s jedním z následujících klientů SSH hostitelského počítače:
   
   **Uživatelé s Windows**
   1. Stáhněte a nainstalujte [PuTTY](http://www.putty.org/) pro systém Windows. 
   1. Zkopírujte IP adresu vašeho pí do název hostitele (nebo IP adresu) oddílu a vyberte jako typ připojení SSH.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac a Ubuntu uživatelů**
   
   Použijte integrovaného klienta SSH na Ubuntu nebo systému macOS. Možná budete muset spustit `ssh pi@<ip address of pi>` připojit platformy prostřednictvím SSH.
   > [!NOTE] 
   Výchozí uživatelské jméno `pi` a heslo je `raspberry`.

1. Nainstalujte Node.js a NPM pro vaše platformy.
   
   Nejprve zkontrolujte verzi Node.js. 
   
   ```bash
   node -v
   ```

   Pokud je verze nižší než 4.x, nebo pokud není žádná Node.js na vaše platformy, nainstalujte nejnovější verzi.

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

1. Naklonujte ukázkovou aplikaci.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

1. Nainstalujte všechny balíčky pro vzorovou. Instalace zahrnuje zařízení Azure IoT SDK, BME280 senzor knihovny a vzájemné propojení pí knihovny.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   Může trvat několik minut na dokončení tohoto procesu instalace v závislosti na připojení k síti.

### <a name="configure-the-sample-application"></a>Nakonfigurujte ukázkovou aplikaci

1. Otevřete konfigurační soubor spuštěním následujících příkazů:

   ```bash
   nano config.json
   ```

   ![Konfigurační soubor](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   Existují dvě položky v tomto souboru, které můžete konfigurovat. První z nich je `interval`, která definuje časový interval (v milisekundách) mezi zprávy odeslané do cloudu. Druhá je `simulatedData`, což je logickou hodnotu pro jestli se má používat data simulované snímačů, nebo ne.

   Pokud jste **nemají senzoru**, nastavte `simulatedData` hodnotu `true` aby ukázkovou aplikaci, vytváření a používání dat snímačů simulované.

1. Uložte a zavřete tak, že zadáte řízení-O > zadejte > CTRL-X.

### <a name="run-the-sample-application"></a>Spuštění ukázkové aplikace

Spuštění ukázkové aplikace tak, že spustíte následující příkaz:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Zkontrolujte, zda jste způsobené kopírováním a vkládáním zařízení připojovací řetězec do jednoduchých uvozovek.


Měli byste vidět následující výstup, který popisuje data snímačů a zprávy, které se odesílají do služby IoT hub.

![Výstup – data snímačů odeslaný malin pí do služby IoT hub](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>Další postup

Spustíte ukázkovou aplikaci pro shromažďování dat snímačů a odeslat do služby IoT hub. Zobrazit zprávy, které vaše platformy malin odeslal do služby IoT hub, nebo k odesílání zpráv do vašeho malin platformy v rozhraní příkazového řádku, podívejte se [cloud zařízení spravovat zasílání zpráv s iothub-explorer kurzu](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
