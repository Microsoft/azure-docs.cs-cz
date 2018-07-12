---
title: Připojte se k Azure IoT Hub ESP8266 do cloudu – Feather HUZZAH ESP8266 | Dokumentace Microsoftu
description: Zjistěte, jak nastavit a připojit Adafruit Feather HUZZAH ESP8266 do služby Azure IoT Hub pro něj k odesílání dat do cloudové platformy Azure v tomto kurzu.
author: rangv
manager: nasing
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 3431cc729550c0dd6eae8f332e2f8996cde9b02f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38235672"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Sada Adafruit Feather HUZZAH ESP8266 připojit ke službě Azure IoT Hub v cloudu

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Připojení mezi DHT22 Feather HUZZAH ESP8266 a IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Co můžete dělat


Sada Adafruit Feather HUZZAH ESP8266 připojení do služby IoT hub, kterou vytvoříte. Potom spustíte ukázkovou aplikaci na ESP8266 ke shromažďování dat teploty a vlhkosti z DHT22 senzoru. A konečně odeslat data ze senzorů do služby IoT hub.

> [!NOTE]
> Pokud používáte jiné panely ESP8266, můžete stále postupujte podle těchto kroků se připojíte ke službě IoT hub. V závislosti na panelu ESP8266 používáte, možná budete muset překonfigurovat `LED_PIN`. Například pokud používáte ESP8266 z AI Thinker, ji můžete změnit z `0` k `2`. Sadu ještě nemáte? Získat z [web Azure](http://azure.com/iotstarterkits).




## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby IoT hub a registrace zařízení pro Feather HUZZAH ESP8266
* Jak se připojit Feather HUZZAH ESP8266 s senzor a počítač
* Jak shromažďovat data ze senzorů pomocí Průvodce ukázkovou aplikaci na Feather HUZZAH ESP8266
* Jak odeslat data ze senzorů do služby IoT hub

## <a name="what-you-need"></a>Co potřebujete

![součásti potřebné pro tento kurz](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

K dokončení této operace, budete potřebovat následující části z Feather HUZZAH ESP8266 Starter Kit:

* Panel Feather HUZZAH ESP8266
* Micro USB kabelu USB typ A

Potřebujete také následující akce pro vaše vývojové prostředí:

* Aktivní předplatné Azure. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Mac nebo počítači se systémem Windows nebo Linuxu.
* Pro Feather HUZZAH ESP8266 pro připojení k bezdrátové síti.
* Připojení k Internetu stáhnout nástroj pro konfiguraci.
* [Rozšíření sady Visual Studio Code pro Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> Rozhraním Arduino IDE verze používané v rozšíření Visual Studio Code pro Arduino musí být ve verzi 1.6.8 nebo novější. Starší verze nefungují s knihovnou AzureIoT.

Následující položky jsou volitelné, v případě, že nemáte senzoru. Máte také možnost používat data ze simulovaných senzorů.

* Senzor teploty a vlhkosti Adafruit DHT22
* Breadboard
* M nebo M můstek vodičům stanice


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Připojit Feather HUZZAH ESP8266 s senzor a počítač
V této části se připojíte k panelu snímačům. Poté připojíte zařízení k počítači pro další použití.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Připojte se k Feather HUZZAH ESP8266 DHT22 senzoru teploty a vlhkosti

Používejte breadboard a můstek vodičům stanice, aby připojení následujícím způsobem. Pokud nemáte senzor, přeskočte tuto část, protože byste mohli použít data ze simulovaných senzorů.

![odkaz na připojení](media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)


Senzor kód PIN použijte následující propojení:


| Spustit (senzor)           | End (panel)           | Barva kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 31F)            | 3v (Připnutí 58H)           | Červené kabel     |
| DATA (Pin 32F)           | GPIO 2 (Pin 46A)       | Modrý kabel    |
| ZEM (Pin 34F)            | ZEM (PIn 56I)          | Černého kabelu pak   |

Další informace najdete v tématu [instalace senzoru Adafruit DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) a [uspořádání Adafruit Feather HUZZAH Esp8266 kolíků](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).



Nyní musí být připojené vaše Feather Huzzah ESP8266 s pracovní senzoru.

![Spojte se s Feather Huzzah DHT22](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Připojte se k počítači Feather HUZZAH ESP8266

Jak je ukázáno dále, Feather HUZZAH ESP8266 připojte k počítači pomocí USB Micro kabelem USB typ A.

![Připojte se k počítači Feather Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Přidání oprávnění sériového portu (pouze Ubuntu)


Pokud používáte Ubuntu, ujistěte se, že máte oprávnění k provozu na USB port z Feather HUZZAH ESP8266. Pokud chcete přidat oprávnění sériového portu, postupujte takto:


1. V terminálu spusťte následující příkazy:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Se zobrazí jedna z následujících výstupy:

   * CRW-rw---xxxxxxxx uucp 1 kořenové
   * CRW-rw---xxxxxxxx síti službou 1 root

   Ve výstupu Všimněte si, že `uucp` nebo `dialout` je název vlastníka skupiny portu USB.

1. Přidejte uživatele do skupiny spuštěním následujícího příkazu:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` je název vlastníka skupiny, které jste získali v předchozím kroku. `<username>` je uživatelské jméno Ubuntu.

1. Odhlaste se z Ubuntu a znovu se přihlaste tato změna se zobrazí.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Shromažďovat data ze senzorů a jeho odeslání do služby IoT hub

V této části nasadit a spustit ukázkovou aplikaci na Feather HUZZAH ESP8266. Ukázková aplikace bliká LED na Feather HUZZAH ESP8266 a odešle teploty a vlhkosti data shromážděná ze snímačů DHT22 do služby IoT hub.

### <a name="get-the-sample-application-from-github"></a>Získání ukázkové aplikace z Githubu

Ukázkové aplikace jsou hostované na Githubu. Naklonujte ukázkové úložiště, která obsahuje ukázkovou aplikaci z Githubu. Naklonujte ukázkové úložiště, postupujte podle těchto kroků:

1. Otevřete příkazový řádek nebo okno terminálu.
1. Přejděte do složky, kam chcete ukázkovou aplikaci, která mají být uloženy.
1. Spusťte následující příkaz:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Instalace balíčku pro Feather HUZZAH ESP8266 ve Visual Studio Code:

1. Otevřete složku, kde uloženy ukázkové aplikace.
1. Otevřete soubor app.ino ve složce aplikace ve Visual Studio Code.

   ![Otevření ukázkové aplikace ve Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

1. Ve Visual Studio Code, zadejte `F1`.
1. Typ **Arduino** a vyberte **Arduino: panel Správce**.
1. V **Arduino panel Správce** klikněte na tlačítko **další adresy URL**.

   ![VS Code Arduino panel Správce](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

1. V **uživatelská nastavení** okna, zkopírujte a vložte následující na konci souboru

   `"arduino.additionalUrls": "http://arduino.esp8266.com/stable/package_esp8266com_index.json"`

   ![Adresa URL balíčku Arduino konfigurace v nástroji VS Code](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

1. Soubor uložte a zavřete **uživatelská nastavení** kartu.
1. Klikněte na tlačítko **aktualizovat balíček indexy**. Po dokončení obnovení vyhledejte **esp8266**.
1. Klikněte na tlačítko **nainstalovat** tlačítko pro esp8266.

   Panely Správce znamená, že je nainstalována ESP8266 s verzí 2.2.0 nebo novější.

   ![Je nainstalovaný balíček esp8266](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

1. Zadejte `F1`, zadejte **Arduino** a vyberte **Arduino: konfigurační panel**.
1. Klikněte na pole pro **panelu vybrali:** a typ **esp8266**a pak vyberte **sada Adafruit HUZZAH ESP8266 (esp8266)**.

   ![Vybrat esp8266 panel](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Nainstalujte potřebné knihovny

1. Ve Visual Studio Code, zadejte `F1`, zadejte **Arduino** a vyberte **Arduino: Správce knihovny**.
1. Vyhledejte následující knihovny názvy jeden po druhém. Pro každou knihovnu, která nenajdete, klikněte na tlačítko **nainstalovat**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nemáte reálných senzorů DHT22?

Ukázkové aplikace můžete simulovat teploty a vlhkosti dat v případě, že nemáte reálných senzorů DHT22. Pokud chcete nainstalovat ukázkovou aplikaci pro použití s Simulovaná data, postupujte podle těchto kroků:

1. Otevřít `config.h` soubor `app` složky.
1. Vyhledejte následující řádek kódu a změňte hodnotu z `false` k `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Nakonfigurovat ukázkovou aplikaci pro použití s Simulovaná data](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

1. Uložte soubor.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Nasazení ukázkové aplikace pro Feather HUZZAH ESP8266

1. Ve Visual Studio Code, klikněte na tlačítko **<Select Serial Port>** stav panelu a potom klikněte na sériového portu pro Feather HUZZAH ESP8266.
1. Zadejte `F1`, zadejte **Arduino** a vyberte **Arduino: nahrání** k sestavení a nasazení ukázkové aplikace pro Feather HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Zadejte přihlašovací údaje.

Po úspěšném dokončení nahrávání, zadejte svoje přihlašovací údaje pomocí těchto kroků:

1. Otevření Arduino IDE, klikněte na tlačítko **nástroje** > **sériového portu monitorování**.
1. V okně monitor sériového portu Všimněte si, že dva rozevírací seznamy v pravém dolním rohu.
1. Vyberte **bez ukončení řádku** pro levé rozevíracího seznamu.
1. Vyberte **115200 přenosová** pro přímo rozevíracího seznamu.
1. Do vstupního pole umístěné v horní části okna monitorování sériového portu, zadejte následující informace, pokud budete vyzváni, zadejte je a potom klikněte na **odeslat**.
   * SSID sítě Wi-Fi
   * Heslo Wi-Fi
   * Připojovací řetězec zařízení

> [!Note]
> Přihlašovací údaje je uložen v EEPROM z Feather HUZZAH ESP8266. Pokud kliknete na tlačítko Obnovit na panel Feather HUZZAH ESP8266, ukázkovou aplikaci zeptá, jestli chcete vymazat informace. Zadejte `Y` vymaže informace. Zobrazí se výzva k zadání informací podruhé.

### <a name="verify-the-sample-application-is-running-successfully"></a>Ověřte, že ukázková aplikace je úspěšně spuštěná.

Pokud se zobrazí následující výstup v okně sériového portu monitorování a blikající Indikátor na Feather HUZZAH ESP8266, ukázkové aplikace je úspěšně spuštěná.

![Finální výstup v rozhraním Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="next-steps"></a>Další postup

Úspěšně jste Feather HUZZAH ESP8266 připojené ke službě IoT hub a data zachycená senzor odeslané do služby IoT hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

