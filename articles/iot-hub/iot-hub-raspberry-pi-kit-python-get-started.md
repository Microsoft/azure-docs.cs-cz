---
title: Malinová pí do cloudu (Python) - pí malin připojit ke službě Azure IoT Hub | Microsoft Docs
description: Zjistěte, jak nastavit a připojení k Azure IoT Hub malin pí k odesílání dat do Azure Cloudová platforma v tomto kurzu malin pí.
author: rangv
manager: ''
keywords: Azure iot Malinová pi, malinová platformy iot hub, malinová pí odesílání dat do cloudu, malinová pí do cloudu
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 15b917d811dff2d70b638ab82c808bdc7fa1012b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635147"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-python"></a>Připojení ke službě Azure IoT Hub (Python) Malinová platformy

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

## <a name="set-up-raspberry-pi"></a>Nastavit malin platformy

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

### <a name="enable-ssh-and-i2c"></a>Povolit SSH a I2C

1. Připojit k monitoru, klávesnice a myši platformy, spusťte platformy a znovu přihlásili Raspbian pomocí `pi` jako uživatelské jméno a `raspberry` jako heslo.
1. Klikněte na ikonu Malinová > **Předvolby** > **malin pí konfigurace**.

   ![V nabídce Raspbian předvolby](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Na **rozhraní** nastavte **I2C** a **SSH** k **povolit**a pak klikněte na **OK**. Pokud nemáte fyzické senzory a chcete použít data simulované snímačů, tento krok je volitelný.

   ![Povolit I2C a SSH na Malinová platformy](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
SSH a I2C povolit, můžete najít další dokumenty odkaz na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) a [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Připojit senzoru PI

Pomocí vedení breadboard a můstek pro připojení DIODU a BME280 PI následujícím způsobem. Pokud nemáte senzoru, [tuto část přeskočte](#connect-pi-to-the-network).

![Připojení malin platformy a senzor](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

BME280 senzoru teploty a vlhkosti data můžete shromažďovat. A DIODU bude blink – Pokud je komunikace mezi zařízením a cloudem. 

Senzor kód PIN použijte následující kabeláž:

| Spuštění (senzor & DIODU)     | End (panelu)            | Kabel barev   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5G)             | 3.3v opravná (Pin 1)       | Bílé kabel   |
| ZEM (Pin 7G)             | ZEM (Pin 6)            | Hnědá kabel   |
| SDI (Pin 10G)            | I2C1 SDA (PIN kódu 3)       | Red kabel     |
| SCK (Pin 8G)             | I2C1 SCL (Pin 5)       | Oranžové kabel  |
| Indikátor VDD (Pin 18F)        | GPIO 24 (Pin 18)       | Bílé kabel   |
| Indikátor zem (Pin 17F)        | ZEM (Pin 20)           | Začernit kabel   |

Kliknutím zobrazíte [malin pí 2 a 3 mapování kódu Pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) pro vaši informaci.

Po připojení BME280 úspěšně vaší malin PI, mělo by být jako níže bitové kopie.

![Připojené platformy a BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Připojení k síti platformy

Zapněte pí pomocí kabelu USB micro a napájení. Pomocí kabelu Ethernet připojit platformy k drátové síti nebo postupujte podle [pokyny z Foundation pí malin](https://www.raspberrypi.org/learning/software-guide/wifi/) pro připojení k bezdrátové síti pí. Po vaší platformy se úspěšně připojil k síti, budete muset poznamenejte [IP adresa vašeho čísla pí](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Připojení k drátové síti](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Ujistěte se, že platformy je připojený ke stejné síti jako počítače. Například pokud je počítač připojen k bezdrátové síti a platformy je připojeno k drátové síti, nemusíte to vidět IP adresu ve výstupu devdisco.

## <a name="run-a-sample-application-on-pi"></a>Spuštění ukázkové aplikace na platformy

### <a name="install-the-prerequisite-packages"></a>Instalaci požadovaných balíčků

Použijte jeden z následujících klientů SSH z hostitelského počítače pro připojení k vaší malin platformy.
   
   **Uživatelé s Windows**
   1. Stáhněte a nainstalujte [PuTTY](http://www.putty.org/) pro systém Windows. 
   1. Zkopírujte IP adresu vašeho pí do název hostitele (nebo IP adresu) oddílu a vyberte jako typ připojení SSH.
   
   
   **Mac a Ubuntu uživatelů**
   
   Použijte integrovaného klienta SSH na Ubuntu nebo systému macOS. Možná budete muset spustit `ssh pi@<ip address of pi>` připojit platformy prostřednictvím SSH.
   > [!NOTE] 
   Výchozí uživatelské jméno `pi` , a heslo je `raspberry`.


### <a name="configure-the-sample-application"></a>Nakonfigurujte ukázkovou aplikaci

1. Naklonujte ukázkovou aplikaci tak, že spustíte následující příkaz:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-python-raspberrypi-client-app.git
   ```
1. Otevřete konfigurační soubor spuštěním následujících příkazů:

   ```bash
   cd iot-hub-python-raspberrypi-client-app
   nano config.py
   ```

   5 maker v tomto souboru můžete configurate. První z nich je `MESSAGE_TIMESPAN`, která definuje časový interval (v milisekundách) mezi dvě zprávy, které odesílají do cloudu. Druhý `SIMULATED_DATA`, což je logickou hodnotu pro jestli se má používat data simulované snímačů, nebo ne. `I2C_ADDRESS` je adresa I2C, která je připojena vaše BME280 senzoru. `GPIO_PIN_ADDRESS` je adresa GPIO pro vaše Indikátor. Je poslední `BLINK_TIMESPAN`, která definována časový interval, po zapnutí vaší DIODU v milisekundách.

   Pokud jste **nemají senzoru**, nastavte `SIMULATED_DATA` hodnotu `True` aby ukázkovou aplikaci, vytváření a používání dat snímačů simulované.

1. Uložte a zavřete stisknutím řízení-O > zadejte > CTRL-X.

### <a name="build-and-run-the-sample-application"></a>Sestavení a spuštění ukázkové aplikace

1. Vytvoření ukázkové aplikace tak, že spustíte následující příkaz. Protože SDK služby Azure IoT pro jazyk Python – obálky nad C SDK zařízení IoT služby Azure, musíte se ke kompilaci knihovny jazyka C, chcete-li nebo nutné vygenerovat Python knihovny ze zdrojového kódu.

   ```bash
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```
   > [!NOTE] 
   Můžete také určit verzi chcete spuštěním `sudo ./setup.sh [--python-version|-p] [2.7|3.4|3.5]`. Pokud spustíte skript bez parametrů, skript automaticky zjistí verzi jazyka python nainstalovaný (pořadí hledání 2.7 -> 3.4 -> 3.5). Ujistěte se, že vaše verze Python zajišťuje konzistentní během vytváření a spouštění. 
   
   > [!NOTE] 
   Na budování Klientská knihovna Python (iothub_client.so) na Linux zařízení, které mají méně než 1GB paměti RAM, mohou se zobrazit sestavení získávání zablokované ve 98 % při sestavování iothub_client_python.cpp, jak je uvedeno níže `[ 98%] Building CXX object python/src/CMakeFiles/iothub_client_python.dir/iothub_client_python.cpp.o`. Pokud narazíte na potíže, zkontrolujte využití paměti na zařízení pomocí `free -m command` v jiné okno terminálu během této doby. Pokud používáte nedostatek paměti při kompilování souborů iothub_client_python.cpp, budete muset dočasně zvětšete velikost místa odkládacího souboru získat úspěšně vytvořit zařízení klienta Python SDK knihovny k dispozici další paměť.
   
1. Spuštění ukázkové aplikace tak, že spustíte následující příkaz:

   ```bash
   python app.py '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Zkontrolujte, zda jste způsobené kopírováním a vkládáním zařízení připojovací řetězec do jednoduchých uvozovek. A pokud používáte python 3, pak můžete použít příkaz `python3 app.py '<your Azure IoT hub device connection string>'`.


   Měli byste vidět následující výstup, který popisuje data snímačů a zprávy, které se odesílají do služby IoT hub.

   ![Výstup – data snímačů odeslaný malin pí do služby IoT hub](media/iot-hub-raspberry-pi-kit-c-get-started/success.png
)

## <a name="next-steps"></a>Další postup

Spustíte ukázkovou aplikaci pro shromažďování dat snímačů a odeslat do služby IoT hub. Zprávy, které vaše platformy malin odeslal na IoT hub nebo odesílání zprávy pro vaše platformy malin v rozhraní příkazového řádku najdete v sekci [cloud zařízení spravovat zasílání zpráv s iothub-explorer kurzu](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
