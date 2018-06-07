---
title: ESP8266 do cloudu - připojení Sparkfun ESP8266 věc Dev do služby Azure IoT Hub | Microsoft Docs
description: Zjistěte, jak nastavit a připojení k Azure IoT Hub pro něj k odesílání dat do Azure Cloudová platforma v tomto kurzu Sparkfun ESP8266 věc vývojářů.
author: rangv
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 75ff53d5be29af08bb8e9c1b41f61040e5710cf7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635317"
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Připojení k Azure IoT Hub v cloudu Sparkfun ESP8266 věc vývojářů

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![připojení mezi DHT22, co vývojářů a IoT Hub](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Co provedete

Sparkfun ESP8266 věc vývojářů připojte do služby IoT hub, které vytvoříte. Pak spusťte ukázkovou aplikaci na ESP8266 ke shromažďování dat teploty a vlhkosti ze DHT22 senzoru. Nakonec odešlete data snímačů do služby IoT hub.

> [!NOTE]
> Pokud používáte jiné ESP8266 panely, můžete stále následujícím postupem pro připojení do služby IoT hub. V závislosti na ESP8266 Tabule, kterou používáte, bude pravděpodobně třeba překonfigurovat `LED_PIN`. Například pokud používáte ESP8266 z AI Thinker, můžete změnit z `0` k `2`. Ještě není hotová kit?: klikněte na tlačítko [sem](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>Co se dozvíte

* Postup vytvoření služby IoT hub a registrovat zařízení za účelem věcí
* Postup připojení věc Dev s senzoru a váš počítač.
* Postup shromažďování dat snímačů spuštěním ukázkovou aplikaci na věc účelem
* Jak odesílat data snímačů do služby IoT hub.

## <a name="what-you-will-need"></a>Co budete potřebovat

![součásti potřebné pro tento kurz](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

Pro dokončení této operace, musíte z vaší věc Dev Starter Kit následujících částí:

* Panel Sparkfun ESP8266 věc vývojářů.
* Micro USB na kabelu USB typ A.

Vývojové prostředí musíte také následující:

* Aktivní předplatné Azure. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Mac nebo počítači se systémem Windows nebo Ubuntu.
* Bezdrátové sítě pro Sparkfun ESP8266 věc vývojářů pro připojení k.
* Připojení k Internetu stahovat nástroj konfigurace.
* [Arduino IDE](https://www.arduino.cc/en/main/software) verze 1.6.8 (nebo novější), dřívější verze nebudou pracovat s knihovnou AzureIoT.

Následující položky jsou volitelné, v případě, že nemáte senzoru. Máte také možnost používat data snímačů simulované.

* Adafruit DHT22 teploty a vlhkosti senzoru.
* Breadboard.
* Vodičům můstek M/M.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Připojit ESP8266 věc Dev s senzoru a počítače

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Senzor teploty a vlhkosti DHT22 připojit k ESP8266 věc vývojářů

Pomocí vedení breadboard a můstek k připojení následujícím způsobem. Pokud nemáte senzoru, tuto část přeskočte, protože data snímačů simulované můžete použít místo.

![odkaz na připojení](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Pro kód PIN senzor použijeme následující kabeláž:

| Spuštění (senzor)           | End (panelu)           | Kabel barev   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27F)            | 3v (8A PIN kódu)           | Red kabel     |
| DATA (Pin 28F)           | GPIO 2 (9A PIN kódu)       | Bílé kabel    |
| ZEM (Pin 30F)            | ZEM (7J kód Pin)          | Začernit kabel   |


- Další informace najdete v tématu: [DHT22 senzor instalace](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) a [specifikace Sparkfun ESP8266 věc vývojářů](https://www.sparkfun.com/products/13711)

Teď vaše Sparkfun ESP8266 věc Dev by měly být připojené s pracovní senzoru.

![připojit dht22 s ESP8266 věc vývojářů](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Připojte k počítači Sparkfun ESP8266 věc vývojářů

USB Micro kabelem USB typ A slouží k připojení Sparkfun ESP8266 věc vývojářů v počítači následujícím způsobem.

![prolnutí huzzah připojte k počítači](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Přidání oprávnění sériového portu – pouze Ubuntu

Pokud používáte Ubuntu, zkontrolujte, zda že normální uživatel má oprávnění k provozu na USB port z Sparkfun ESP8266 věc účelem Pokud chcete přidat oprávnění sériového portu pro běžné uživatele, postupujte takto:

1. V terminálu, spusťte následující příkazy:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Zobrazí jednu z následujících výstupy:

   * CRW-rw---1 kořenové uucp xxxxxxxx
   * CRW-rw---xxxxxxxx síti službou 1 root

   Ve výstupu, Všimněte si `uucp` nebo `dialout` který je vlastníkem název skupiny portu USB.

1. Přidejte uživatele do skupiny tak, že spustíte následující příkaz:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` je název vlastníka skupiny, kterou jste získali v předchozím kroku. `<username>` je Ubuntu uživatelské jméno.

1. Odhlaste se Ubuntu a přihlaste se znovu pro změna se projeví.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Shromažďování dat snímačů a odeslat do služby IoT hub

V této části můžete nasazení a spuštění ukázkové aplikace na Sparkfun ESP8266 věc účelem Ukázkovou aplikaci bliká DIODU na Sparkfun ESP8266 věc vývojářů a odešle teploty a vlhkosti data shromážděná z senzoru DHT22 do služby IoT hub.

### <a name="get-the-sample-application-from-github"></a>Získat ukázkovou aplikaci z webu GitHub

Ukázkové aplikace jsou hostované na Githubu. Naklonujte úložiště ukázka, která obsahuje ukázkovou aplikaci z webu GitHub. Klonovat úložiště ukázkové, postupujte takto:

1. Otevřete příkazový řádek nebo okno terminálu.
1. Přejděte do složky, kam chcete ukázkovou aplikaci k uložení.
1. Spusťte následující příkaz:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Instalace balíčku pro vývojáře věc ESP8266 Sparkfun v integrovaném vývojovém prostředí Arduino:

1. Otevřete složku, kde je uložen ukázkovou aplikaci.
1. Otevřete soubor app.ino ve složce aplikace v integrovaném vývojovém prostředí Arduino.

   ![Otevřete ukázkovou aplikaci v arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. V prostředí IDE Arduino, klikněte na tlačítko **soubor** > **Předvolby**.
1. V **Předvolby** dialogové okno pole, klikněte na ikonu vedle **další adresy URL Manager panely** textové pole.
1. V místním okně, zadejte následující adresu URL a pak klikněte na tlačítko **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![přejděte na adresu url balíčku v arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. V **předvoleb** dialogové okno, klikněte na tlačítko **OK**.
1. Klikněte na tlačítko **nástroje** > **Tabule** > **Manager panely**a poté vyhledejte esp8266.
   Musí být nainstalován ESP8266 verzí 2.2.0 nebo novější.

   ![instalaci balíčku esp8266](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Klikněte na tlačítko **nástroje** > **Tabule** > **Sparkfun ESP8266 věc Dev**.

### <a name="install-necessary-libraries"></a>Instalace nezbytné knihovny

1. V prostředí IDE Arduino, klikněte na tlačítko **nákresu** > **zahrnují knihovny** > **spravovat knihovny**.
1. Vyhledejte následující knihovny názvy po jednom. Pro každou z knihovny zjistíte, klikněte na tlačítko **nainstalovat**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nemáte skutečné senzor DHT22?

Ukázkové aplikace můžete simulovat teploty a vlhkosti dat v případě, že nemáte skutečné DHT22 senzoru. Pokud chcete povolit ukázkovou aplikaci pro simulované data použít, postupujte takto:

1. Otevřete `config.h` v soubor `app` složky.
1. Vyhledejte následující řádek kódu a změňte hodnotu z `false` k `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Nakonfigurujte ukázkovou aplikaci používat simulované dat](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Uložit s `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Nasadit ukázkovou aplikaci pro Sparkfun ESP8266 věc vývojářů

1. V prostředí IDE Arduino, klikněte na tlačítko **nástroj** > **Port**a potom klikněte na sériového portu za účelem co ESP8266 Sparkfun
1. Klikněte na tlačítko **nákresu** > **nahrát** sestavení a nasazení ukázkové aplikace na Sparkfun ESP8266 věc účelem

> [!Note]
> Pokud používáte systému macOS během nahrávání se pravděpodobně může zobrazit následující zprávy. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Otevřete okno vaší ternimal a dokončit následující akce pro tento problém vyřešit.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Zadejte přihlašovací údaje.

Po úspěšném dokončení nahrávání, postupujte podle kroků k zadání přihlašovacích údajů:

1. V prostředí IDE Arduino, klikněte na tlačítko **nástroje** > **sériové monitorování**.
1. V okně serial monitorování Všimněte si dva rozevírací seznamy v pravém horním rohu.
1. Vyberte **žádný řádek ukončování** pro levý rozevíracího seznamu.
1. Vyberte **115200 přenosová** pro právo rozevíracího seznamu.
1. Do vstupního pole umístěné v horní části okna sériové sledování, zadejte následující informace, pokud se zobrazí výzva k poskytování je a pak klikněte na tlačítko **odeslat**.
   * SSID sítě Wi-Fi
   * Heslo Wi-Fi
   * Řetězec připojení zařízení

> [!Note]
> Informace o přihlašovacích údajích je uložené v paměti EEPROM ESP8266 věc účelem Sparkfun Pokud kliknete na tlačítko Obnovit na panelu Sparkfun ESP8266 věc vývojářů, ukázkové aplikace zobrazí dotaz, pokud chcete vymazat informace. Zadejte `Y` tak, aby měl informace vymazat a budete vyzváni k zadání informace znovu.

### <a name="verify-the-sample-application-is-running-successfully"></a>Zkontrolujte, zda že ukázkové aplikace je úspěšně spuštěna.

Pokud se zobrazí následující výstup z okna sériové monitorování a blikající LED na Sparkfun ESP8266 věc vývojářů, ukázkové aplikace je úspěšně spuštěna.

![závěrečný výstup v integrovaném vývojovém prostředí arduino](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Další postup

Úspěšně jste připojené Sparkfun ESP8266 věc Dev do služby IoT hub a data zaznamenaná senzor odeslané do služby IoT hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
