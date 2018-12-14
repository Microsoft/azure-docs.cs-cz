---
title: Raspberry Pi do cloudu (Node.js) – připojte Raspberry Pi pro službu Azure IoT Hub | Dokumentace Microsoftu
description: Zjistěte, jak nastavit a Raspberry Pi připojit ke službě Azure IoT Hub pro Raspberry Pi k odesílání dat do cloudové platformy Azure v tomto kurzu.
author: rangv
manager: ''
keywords: Azure iot raspberry pi, raspberry pi iot hub, raspberry pi odesílání dat do cloudu, raspberry pi s cloudem
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 8a533b3bf90ac2635ee05c6511c28d18784ddfec
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343849"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Připojte Raspberry Pi pro službu Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu začnete pomocí seznámení se základy práce s Raspberry Pi, na kterém běží Raspbian. Pak se naučíte bez problémů propojit si zařízení do cloudu s využitím [Azure IoT Hub](about-iot-hub.md). Ukázky Windows 10 IoT Core najdete [Windows Dev Center](http://www.windowsondevices.com/).

Sadu ještě nemáte? Zkuste [online simulátor Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Nebo zakoupit sadu nových [tady](https://azure.microsoft.com/develop/iot/starter-kits).


## <a name="what-you-do"></a>Co můžete dělat

* Vytvoření služby IoT hub.
* Registrace zařízení ve službě IoT hub pro číslo pí.
* Nastavte Raspberry Pi.
* Spusťte ukázkovou aplikaci v Pi odeslat data ze senzorů do služby IoT hub.

## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby Azure IoT hub a získat nový připojovací řetězec zařízení.
* Jak se připojit Pi s BME280 senzoru.
* Jak shromažďovat data ze senzorů spuštěním ukázkové aplikace na číslo pí.
* Jak odeslat data ze senzorů do služby IoT hub.

## <a name="what-you-need"></a>Co potřebujete

![Co potřebujete](./media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* Desce Raspberry Pi 2 nebo Raspberry Pi 3.
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* Monitorování, USB klávesnici a myš, která se připojuje k pí.
* Mac nebo počítači se systémem Windows nebo Linux.
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
> Pokud nemáte k dispozici volitelné komponenty, můžete použít data ze simulovaných senzorů.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro službu IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zaregistrujte nové zařízení ve službě IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Nastavit Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalace operačního systému Raspbian Pi

Příprava pro instalaci bitové kopie Raspbian karty microSD.

1. Stáhněte si Raspbian.

   a. [Stáhněte si Raspbian Stretch](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (soubor .zip).

   > [!WARNING]
   > Použijte prosím nad odkaz ke stažení `raspbian-2017-07-5` zip image. Nejnovější verzi Image Raspbian má několik známých problémů v uzlu její Pi, což by mohlo způsobit selhání v dalších krocích.
 
   b. Extrahujte Raspbian image do složky v počítači.

2. Instalace na kartě microSD Raspbian.

   a. [Stáhněte a nainstalujte nástroj zapisovací jednotka karty Etcher SD](https://etcher.io/).

   b. Spusťte Etcher a vyberte Raspbian bitové kopie, které jste extrahovali v kroku 1.

   c. Vyberte jednotku microSD karta. Etcher může mít již vybrána správná jednotka.

   d. Klepněte na Flash instalace Raspbian microSD karta.

   e. Karta microSD neodeberete z počítače, po dokončení instalace. Jde bezpečně odebrat microSD karta přímo, protože Etcher automaticky vysune nebo odpojí microSD karta po dokončení.

   f. Vložte karty microSD do pí.

### <a name="enable-ssh-and-i2c"></a>Povolit SSH a I2C

1. Pi se připojte k monitoru, klávesnice a myši. 

2. Spuštění číslo pí a přihlaste Raspbian pomocí `pi` jako uživatelské jméno a `raspberry` jako heslo.

3. Klikněte na ikonu Raspberry > **Předvolby** > **Raspberry Pi konfigurace**.

   ![V nabídce Předvolby Raspbian](./media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

4. Na **rozhraní** kartu, nastavte **I2C** a **SSH** k **povolit**a potom klikněte na tlačítko **OK**. Pokud nemáte fyzické senzory a chcete použít data ze simulovaných senzorů, tento krok je volitelný.

   ![Povolit I2C a SSH v Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
> Pokud chcete povolit SSH a I2C, najdete další referenční dokumenty na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) a [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Připojit senzor PI

Breadboard a můstek vodičům stanice slouží k připojení kontrolku LED a BME280 PI následujícím způsobem. Pokud nemáte senzor [tuto část přeskočit](#connect-pi-to-the-network).

![Připojení Raspberry Pi a snímače](./media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

Snímač BME280 může shromažďovat data o teplotě a vlhkosti. Indikátor LED bliká zařízení odešle zprávu do cloudu. 

Senzor kód PIN použijte následující propojení:

| Spustit (senzor & Indikátor)     | End (panel)            | Barva kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5G)             | 3,3 opravná (Pin 1)       | Bílé kabel   |
| ZEM (Pin 7G)             | ZEM (Pin 6)            | Hnědá kabel   |
| SDI (Pin 10G)            | I2C1 SDA (Pin 3)       | Červené kabel     |
| SCK (Pin 8G)             | I2C1 SCL (Pin 5)       | Oranžové kabel  |
| Indikátor VDD (Pin 18F)        | GPIO 24 (Pin 18)       | Bílé kabel   |
| Indikátor zem (Pin 17F)        | ZEM (Pin 20)           | Černého kabelu pak   |

Kliknutím zobrazíte [Raspberry Pi 2 a 3 mapování kódu pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) pro vaši informaci.

Poté, co právě jste úspěšně propojili BME280 Raspberry Pi, měla by být jako obrázku níže.

![Připojené Pi a BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Připojit k síti, Pi

Zapněte pí pomocí USB kabelu micro a napájení. Pomocí kabelu Ethernet Pi připojení k drátové síti ani postupovat podle pokynů [pokyny od Raspberry Pi základ](https://www.raspberrypi.org/learning/software-guide/wifi/) Pi připojit k bezdrátové síti. Až váš Pi byl úspěšně připojen k síti, budete muset poznamenejte si [IP adresu vašeho pí](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Připojený k drátové síti](./media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Ujistěte se, že Pi je připojený ke stejné síti jako počítače. Například pokud je počítač připojen k bezdrátové síti Pi je připojené k drátové síti, nemusíte to vidět IP adresu ve výstupu devdisco.

## <a name="run-a-sample-application-on-pi"></a>Spustit ukázkovou aplikaci v Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Naklonujte ukázkovou aplikaci a nainstalujte požadované balíčky

1. Připojení k Raspberry Pi s jedním z následujících klientů SSH z hostitelského počítače:
   
   **Uživatelé Windows**
  
   a. Stáhněte a nainstalujte [PuTTY](http://www.putty.org/) pro Windows. 

   b. Zkopírujte IP adresu části Pi do názvu hostitele (nebo IP adresu) a vyberte jako typ připojení SSH.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac a uživatelé Ubuntu**
   
   Pomocí integrovaného klienta SSH v systému Ubuntu nebo macOS. Možná budete muset spustit `ssh pi@<ip address of pi>` připojit Pi přes protokol SSH.

   > [!NOTE] 
   > Výchozí uživatelské jméno `pi` a heslo je `raspberry`.

2. Nainstalujte Node.js a NPM na vaše číslo pí.
   
   Nejprve zkontrolujte verzi Node.js. 
   
   ```bash
   node -v
   ```

   Pokud je verze nižší než 4.x nebo pokud neexistuje žádný Node.js na vaše číslo pí, nainstalujte nejnovější verzi.

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Naklonujte ukázkovou aplikaci.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

4. Instalace všech balíčků pro vzorku. Instalace zahrnuje zařízení Azure IoT SDK, knihovny BME280 senzor a vzájemné propojení Pi knihovny.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   >Může trvat několik minut na dokončení tohoto procesu instalace v závislosti na připojení k síti.

### <a name="configure-the-sample-application"></a>Nakonfigurovat ukázkovou aplikaci

1. Otevřete konfigurační soubor spuštěním následujících příkazů:

   ```bash
   nano config.json
   ```

   ![Konfigurační soubor](./media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   Existují dvě položky v tomto souboru, která můžete konfigurovat. První z nich je `interval`, která definuje časový interval (v milisekundách) mezi zprávy odeslané do cloudu. Druhá je `simulatedData`, což je logická hodnota, pro, jestli se má použít data ze simulovaných senzorů nebo ne.

   Pokud jste **nemají senzor**, nastavte `simulatedData` hodnota, která se `true` ukázkové aplikaci vytvořit a používat data ze simulovaných senzorů.

2. Uložte a zavřete tak, že zadáte O ovládací prvek > Enter > X ovládacího prvku.

### <a name="run-the-sample-application"></a>Spuštění ukázkové aplikace

Spuštění ukázkové aplikace spuštěním následujícího příkazu:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Ujistěte se, že je kopírování a vkládání připojovací řetězec zařízení v jednoduchých uvozovkách.


Měli byste vidět následující výstup, který zobrazuje data ze senzorů a zprávy, které se odesílají do služby IoT hub.

![Výstup – data ze senzorů odeslané do služby IoT hub z Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>Další postup

Jste spustili ukázkovou aplikaci shromažďovat data ze senzorů a odesílat je do služby IoT hub. Zprávy, které služba IoT hub nebo odesílání zpráv do Raspberry Pi odeslal Raspberry Pi najdete v tématu [použití sady Azure IoT Hub Toolkit rozšíření pro Visual Studio Code k odesílání a příjem zpráv mezi zařízením a centrem IoT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
