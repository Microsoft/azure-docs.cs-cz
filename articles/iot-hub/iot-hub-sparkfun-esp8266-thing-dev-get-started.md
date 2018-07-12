---
title: ESP8266 do cloudu – připojit Sparkfun ESP8266 Thing Dev do služby Azure IoT Hub | Dokumentace Microsoftu
description: Zjistěte, jak nastavit a připojit Sparkfun ESP8266 Thing Dev do služby Azure IoT Hub pro něj k odesílání dat do cloudové platformy Azure v tomto kurzu.
author: rangv
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 75ff53d5be29af08bb8e9c1b41f61040e5710cf7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452511"
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Sada Sparkfun ESP8266 Thing Dev připojit ke službě Azure IoT Hub v cloudu

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![připojení mezi DHT22 Thing Dev a IoT Hub](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Co budete dělat

Sada Sparkfun ESP8266 Thing Dev připojení do služby IoT hub, kterou vytvoříte. Pak spusťte ukázkovou aplikaci na ESP8266 shromažďovat údaje o teplotě a vlhkosti DHT22 senzoru. A konečně odešlete data ze senzorů do služby IoT hub.

> [!NOTE]
> Pokud používáte jiné panely ESP8266, můžete stále postupujte podle těchto kroků se připojíte ke službě IoT hub. V závislosti na panelu ESP8266 použijete, budete muset překonfigurovat `LED_PIN`. Například pokud používáte ESP8266 z AI Thinker, můžete změnit z `0` k `2`. Ještě nemáte sadu?: klikněte na tlačítko [zde](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>Co se dozvíte

* Postup vytvoření služby IoT hub a registrace zařízení pro věc. odch.
* Jak se připojit Thing Dev s senzor a počítače.
* Jak shromažďovat data ze senzorů pomocí Průvodce ukázkovou aplikaci na věc. odch.
* Jak odeslat data ze senzorů do služby IoT hub.

## <a name="what-you-will-need"></a>Co budete potřebovat

![součásti potřebné pro tento kurz](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

K dokončení této operace, budete potřebovat následující části z vaší Thing Dev Starter Kit:

* Sada Sparkfun ESP8266 Thing Dev panelu.
* Micro USB typ A USB kabelu.

Pro vývojové prostředí potřebujete také následující:

* Aktivní předplatné Azure. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Mac nebo počítači se systémem Windows nebo Linuxu.
* Pro Sparkfun ESP8266 Thing Dev pro připojení k bezdrátové síti.
* Připojení k Internetu stáhnout nástroj pro konfiguraci.
* [Rozhraním Arduino IDE](https://www.arduino.cc/en/main/software) verze 1.6.8 (nebo novější), starší verze nebude fungovat s knihovnou AzureIoT.

Následující položky jsou volitelné, v případě, že nemáte senzoru. Máte také možnost používat data ze simulovaných senzorů.

* Adafruit DHT22 teploty a vlhkosti senzoru.
* Breadboard.
* M nebo M můstek vodičům stanice.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Připojit ESP8266 Thing Dev s senzor a počítač

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Připojte se k ESP8266 Thing Dev DHT22 senzoru teploty a vlhkosti

Používejte breadboard a můstek vodičům stanice, aby připojení následujícím způsobem. Pokud nemáte senzor, přeskočte tuto část, protože byste mohli použít data ze simulovaných senzorů.

![odkaz na připojení](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Senzor kód PIN budeme používat následující propojení:

| Spustit (senzor)           | End (panel)           | Barva kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27F)            | 3v (8A kódu Pin)           | Červené kabel     |
| DATA (Pin 28F)           | GPIO 2 (9A kódu Pin)       | Bílé kabel    |
| ZEM (Pin 30F)            | ZEM (7J kódu Pin)          | Černého kabelu pak   |


- Další informace najdete v tématu: [instalace senzoru DHT22](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) a [specifikace Sparkfun ESP8266 Thing Dev](https://www.sparkfun.com/products/13711)

Nyní musí být připojené vaše sada Sparkfun ESP8266 Thing Dev s pracovní senzoru.

![napojení dht22 ESP8266 Thing Dev](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Sada Sparkfun ESP8266 Thing Dev připojte k počítači

Pomocí USB Micro kabelem USB typ A následujícím způsobem připojte Sparkfun ESP8266 Thing Dev k počítači.

![Připojte se k počítači feather huzzah](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Přidání oprávnění sériového portu – pouze Ubuntu

Pokud používáte Ubuntu, ujistěte se, že běžný uživatel má oprávnění k provozování na USB port z Sparkfun ESP8266 Thing. odch. Přidání sériových portů. oprávnění pro běžného uživatele, postupujte podle těchto kroků:

1. V terminálu spusťte následující příkazy:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Se zobrazí jedna z následujících výstupy:

   * CRW-rw---xxxxxxxx uucp 1 kořenové
   * CRW-rw---xxxxxxxx síti službou 1 root

   Ve výstupu Všimněte si, že `uucp` nebo `dialout` , který je vlastníkem názvu skupiny portu USB.

1. Přidejte uživatele do skupiny spuštěním následujícího příkazu:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` je název vlastníka skupiny, které jste získali v předchozím kroku. `<username>` je uživatelské jméno Ubuntu.

1. Odhlásit se systémem Ubuntu a přihlaste se znovu pro změna projevila.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Shromažďovat data ze senzorů a jeho odeslání do služby IoT hub

V této části nasazení a spuštění ukázkové aplikace ve vývojovém prostředí. sada Sparkfun ESP8266 Thing Ukázková aplikace bliká LED na Sparkfun ESP8266 Thing Dev a odešle teploty a vlhkosti data shromážděná ze snímačů DHT22 do služby IoT hub.

### <a name="get-the-sample-application-from-github"></a>Získání ukázkové aplikace z Githubu

Ukázkové aplikace jsou hostované na Githubu. Naklonujte ukázkové úložiště, která obsahuje ukázkovou aplikaci z Githubu. Naklonujte ukázkové úložiště, postupujte podle těchto kroků:

1. Otevřete příkazový řádek nebo okno terminálu.
1. Přejděte do složky, kam chcete ukázkovou aplikaci, která mají být uloženy.
1. Spusťte následující příkaz:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Instalace balíčku pro Sparkfun ESP8266 Thing Dev v rozhraním Arduino IDE:

1. Otevřete složku, kde uloženy ukázkové aplikace.
1. Otevřete soubor app.ino ve složce aplikace v rozhraním Arduino IDE.

   ![Otevřete ukázkovou aplikaci v rozhraním arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. V rozhraním Arduino IDE, klikněte na tlačítko **souboru** > **Předvolby**.
1. V **Předvolby** dialogového okna klikněte ikonu vedle **další správce adresy URL panely** textového pole.
1. V místním okně zadejte následující adresu URL a pak klikněte na tlačítko **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![přejděte na adresu url balíčku v rozhraním arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. V **předvoleb** dialogové okno, klikněte na tlačítko **OK**.
1. Klikněte na tlačítko **nástroje** > **panelu** > **panely správce**a poté vyhledejte esp8266.
   Musí být nainstalován ESP8266 s verzí 2.2.0 nebo novější.

   ![Je nainstalovaný balíček esp8266](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Klikněte na tlačítko **nástroje** > **panelu** > **Sparkfun ESP8266 Thing Dev**.

### <a name="install-necessary-libraries"></a>Nainstalujte potřebné knihovny

1. V rozhraním Arduino IDE, klikněte na tlačítko **Sketch** > **zahrnout knihovny** > **spravovat knihovny**.
1. Vyhledejte následující knihovny názvy jeden po druhém. Pro každou z knihovny nenajdete, kliknutím na **nainstalovat**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nemáte reálných senzorů DHT22?

Ukázkové aplikace můžete simulovat teploty a vlhkosti dat v případě, že nemáte reálných senzorů DHT22. Pokud chcete povolit ukázkovou aplikaci pro použití s Simulovaná data, postupujte podle těchto kroků:

1. Otevřít `config.h` soubor `app` složky.
1. Vyhledejte následující řádek kódu a změňte hodnotu z `false` k `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Nakonfigurovat ukázkovou aplikaci pro použití s Simulovaná data](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Ušetřete s `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Nasazení ukázkové aplikace pro Sparkfun ESP8266 Thing Dev

1. V rozhraním Arduino IDE, klikněte na tlačítko **nástroj** > **Port**a potom klikněte na sériového portu pro Sparkfun ESP8266 Thing prostředí Dev.
1. Klikněte na tlačítko **Sketch** > **nahrát** k sestavení a nasazení ukázkové aplikace pro Sparkfun ESP8266 Thing prostředí Dev.

> [!Note]
> Pokud používáte macOS může zobrazit následující zprávy pravděpodobně během nahrávání. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Otevřete okno ternimal a dokončit následující akce k vyřešení tohoto problému.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Zadejte přihlašovací údaje.

Po úspěšném dokončení nahrávání, postupujte podle kroků k zadání přihlašovacích údajů:

1. V rozhraním Arduino IDE, klikněte na tlačítko **nástroje** > **sériového portu monitorování**.
1. V okně monitor sériového portu Všimněte si, že dva rozevírací seznamy v pravém dolním rohu.
1. Vyberte **bez ukončení řádku** pro levé rozevíracího seznamu.
1. Vyberte **115200 přenosová** pro přímo rozevíracího seznamu.
1. Do vstupního pole umístěné v horní části okna monitorování sériového portu, zadejte následující informace, pokud budete vyzváni, zadejte je a potom klikněte na **odeslat**.
   * SSID sítě Wi-Fi
   * Heslo Wi-Fi
   * Připojovací řetězec zařízení

> [!Note]
> Přihlašovací údaje je uložen v EEPROM z Sparkfun ESP8266 Thing. odch. Pokud kliknete na tlačítko Obnovit na panelu Sparkfun ESP8266 Thing Dev, ukázkovou aplikaci vás zeptá, jestli chcete vymazat informace. Zadejte `Y` mít informace vymaže a budete vyzváni k zadání informace znovu.

### <a name="verify-the-sample-application-is-running-successfully"></a>Ověřte, že ukázková aplikace je úspěšně spuštěná.

Pokud se zobrazí následující výstup v okně sériového portu monitorování a blikající Indikátor na Sparkfun ESP8266 Thing Dev, ukázkové aplikace je úspěšně spuštěná.

![finální výstup v rozhraním arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Další postup

Úspěšně připojeno Sparkfun ESP8266 Thing Dev do služby IoT hub a data zachycená senzor odeslané do služby IoT hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
