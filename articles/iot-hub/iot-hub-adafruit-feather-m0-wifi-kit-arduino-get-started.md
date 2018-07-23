---
title: Připojte se k Azure IoT Hub m0 do cloudu – Feather M0 WiFi | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a připojit se k Azure IoT Hub pro odesílání dat do cloudové platformy Azure v tomto kurzu Adafruit Feather M0 WiFi.
author: rangv
manager: nasing
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 46763cbc53f70e25810a9849b71429a315a6fb7d
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185359"
---
# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Adafruit Feather M0 WiFi se připojit ke službě Azure IoT Hub v cloudu
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Připojení mezi BME280, Feather M0 WiFi a IoT Hub](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

V tomto kurzu začnete pomocí seznámení se základy práce s rozhraním Arduino panelu. Pak se naučíte bez problémů propojit si zařízení do cloudu s využitím [Azure IoT Hub](about-iot-hub.md).

## <a name="what-you-do"></a>Co můžete dělat

Sada Adafruit Feather M0 WiFi připojení do služby IoT hub, kterou vytvoříte. Potom spustíte ukázkovou aplikaci na M0 WiFi ke shromažďování dat teploty a vlhkosti z BME280. A konečně odeslat data ze senzorů do služby IoT hub.


## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby IoT hub a registrace zařízení pro Feather M0 WiFi
* Jak se připojit Feather M0 WiFi s senzor a počítač
* Jak shromažďovat data ze senzorů pomocí Průvodce ukázkovou aplikaci na Feather M0 WiFi
* Jak odeslat data ze senzorů do služby IoT hub

## <a name="what-you-need"></a>Co potřebujete

![součásti potřebné pro tento kurz](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

K dokončení této operace, budete potřebovat následující části z Feather M0 WiFi Starter Kit:

* Panel Feather M0 WiFi
* Micro USB kabelu USB typ A

Potřebujete také následující akce pro vaše vývojové prostředí:

* Aktivní předplatné Azure. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Mac nebo počítači se systémem Windows nebo Linuxu.
* Pro Feather M0 WiFi se připojit k bezdrátové síti.
* Připojení k Internetu stáhnout nástroj pro konfiguraci.
* [Rozhraním Arduino IDE](https://www.arduino.cc/en/main/software) verze 1.6.8 nebo novější. Starší verze nefungují s knihovnou Azure IoT Hub.

Pokud nemáte senzor, následující položky jsou volitelné. Máte také možnost používat data ze simulovaných senzorů:

* Senzor teploty a vlhkosti BME280
* Breadboard
* M nebo M můstek vodičům stanice

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Spojte se s senzor a počítač Feather M0 WiFi
V této části se připojíte k panelu snímačům. Poté připojíte zařízení k počítači pro další použití.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Připojit senzoru teploty a vlhkosti DHT22 Feather M0 WiFi

Používejte breadboard a můstek vodičům stanice, aby připojení. Pokud nemáte senzor, přeskočte tuto část, protože byste mohli použít data ze simulovaných senzorů.

![odkaz na připojení](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Senzor kód PIN použijte následující propojení:


| Spuštění (senzor)           | End (panel)            | Barva kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27A)            | 3v (3A kódu Pin)            | Červené kabel     |
| ZEM (Pin 29A)            | ZEM (6A kódu Pin)           | Černého kabelu pak   |
| SCK (Pin 30A)            | SCK (Pin 12A)          | Žlutý kabel  |
| SDO (Pin 31A)            | MI (Pin 14A)           | Bílé kabel   |
| SDI (Pin 32A)            | M0 (Pin 13A)           | Modrý kabel    |
| CS (Pin 33A)             | GPIO 5 (Pin 15J)       | Oranžové kabel  |

Další informace najdete v tématu [vlhkosti BME280 Adafruit barometrický tlak + teplotní snímač užitečných](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) a [uspořádání Adafruit Feather M0 WiFi kolíků](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Nyní musí být připojené vaše Feather M0 WiFi s pracovní senzoru.

![Spojte se s Feather Huzzah DHT22](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Připojte se k počítači Feather M0 WiFi

Pomocí USB Micro kabelem USB typ A Feather M0 WiFi se připojit k počítači, jak je znázorněno:

![Připojte se k počítači Feather Huzzah](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Přidání oprávnění sériového portu (pouze Ubuntu)

Pokud používáte Ubuntu, ujistěte se, že máte oprávnění k provozu na USB port z Feather M0 WiFi. Pokud chcete přidat oprávnění sériového portu, postupujte takto:


1. V terminálu spusťte následující příkazy:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Se zobrazí jedna z následujících výstupy:

   * CRW-rw---xxxxxxxx uucp 1 kořenové
   * CRW-rw---xxxxxxxx síti službou 1 root

   Ve výstupu Všimněte si, že `uucp` nebo `dialout` je název vlastníka skupiny portu USB.

2. Chcete-li přidat uživatele do skupiny, spusťte následující příkaz:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   V předchozím kroku jste získali název vlastníka skupiny `<group-owner-name>`. Uživatelské jméno Ubuntu `<username>`.

3. Tato změna se zobrazí, odhlaste se z Ubuntu a znovu se přihlaste.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Shromažďovat data ze senzorů a jeho odeslání do služby IoT hub

V této části nasadit a spustit ukázkovou aplikaci na Feather M0 WiFi. Ukázková aplikace vytváří blikání Indikátor na Feather M0 WiFi. Potom pošle teploty a vlhkosti data shromážděná ze snímačů BME280 do služby IoT hub.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>Získání ukázkové aplikace z Githubu a příprava rozhraním Arduino IDE

Ukázkové aplikace jsou hostované na Githubu. Naklonujte ukázkové úložiště, která obsahuje ukázkovou aplikaci z Githubu. Naklonujte ukázkové úložiště, postupujte podle těchto kroků:

1. Otevřete příkazový řádek nebo okno terminálu.

2. Přejděte do složky, kam chcete ukázkovou aplikaci, která mají být uloženy.
3. Spusťte následující příkaz:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>Instalace balíčku pro Feather M0 WiFi v rozhraním Arduino IDE

1. Otevřete složku, kde uloženy ukázkové aplikace.

2. Otevřete soubor app.ino ve složce aplikace v rozhraním Arduino IDE.

   ![Otevřete ukázkovou aplikaci v rozhraním Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. Klikněte na tlačítko **souboru** > **Předvolby** (Windows/Linux) nebo **Arduino** > **Předvolby** (Mac) a zkopírujte a Vložte odkaz do **další správce adresy URL panely** možnost v předvolbách rozhraním Arduino IDE.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. Klikněte na tlačítko **nástroje** > **panelu** > **panely správce**a pak nainstalujte `Arduino SAMD Boards` verze `1.6.2` nebo novější. 

1. Ve stejném okně nainstalujte `Adafruit SAMD Boards` balíček, který chcete přidat soubor definice panelu.

   ![Je nainstalovaný balíček esp8266](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. Klikněte na tlačítko **nástroje** > **panelu** > **Adafruit M0 WiFi**.

5. Instalace ovladačů (jen Windows). Když připojíte Feather M0 WiFi, potřebujete nainstalovat ovladač. Klikněte na tlačítko [odkaz ke stažení na webové stránce](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) pro stažení instalačního programu ovladače. Postupujte podle pokynů a nainstalujte ovladače, které chcete.

### <a name="install-necessary-libraries"></a>Nainstalujte potřebné knihovny

1. V rozhraním Arduino IDE, klikněte na tlačítko **Sketch** > **zahrnout knihovny** > **spravovat knihovny**.

2. Vyhledejte následující knihovny názvy jeden po druhém. Pro každou knihovnu, která nenajdete, klikněte na tlačítko **nainstalovat**:

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. Ruční instalace `Adafruit_WINC1500`. Přejděte na [tento web](https://github.com/adafruit/Adafruit_WINC1500) a klikněte na tlačítko **klonovat nebo stáhnout** > **stáhnout ZIP**. Potom rozhraním Arduino IDE, přejděte na **Sketch** > **zahrnout knihovny** > **přidat ZIP knihovny** a přidejte tento soubor zip.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>Pokud nemáte reálných senzorů BME280 pomocí ukázkové aplikace

Pokud nemáte reálných senzorů BME280, ukázkové aplikace můžete simulovat teploty a vlhkosti data. Pokud chcete nainstalovat ukázkovou aplikaci pro použití s Simulovaná data, postupujte podle těchto kroků:

1. Otevřít `config.h` soubor `app` složky.

2. Vyhledejte následující řádek kódu a změňte hodnotu z `false` k `true`:

   ```c
   define SIMULATED_DATA true
   ```
   ![Nakonfigurovat ukázkovou aplikaci pro použití s Simulovaná data](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. Uložte soubor s `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Nasazení ukázkové aplikace pro Feather M0 WiFi

1. V rozhraním Arduino IDE, klikněte na tlačítko **nástroj** > **Port**a potom klikněte na sériového portu pro Feather M0 WiFi.

2. Klikněte na tlačítko **Sketch** > **nahrát** k sestavení a nasazení ukázkové aplikace pro Feather M0 WiFi.

### <a name="enter-your-credentials"></a>Zadejte přihlašovací údaje.

Po úspěšném dokončení nahrávání, zadejte svoje přihlašovací údaje pomocí těchto kroků:

1. V rozhraním Arduino IDE, klikněte na tlačítko **nástroje** > **sériového portu monitorování**.

2. V pravém dolním rohu okna sériového portu monitorování, vyberte **bez ukončení řádku** v rozevíracím seznamu na levé straně.
3. Vyberte **115200 přenosová** v rozevíracím seznamu na pravé straně.
4. Do vstupního pole v horní části, zadejte následující informace, pokud budete vyzváni, zadejte ho a klikněte na **odeslat**:

   * SSID sítě Wi-Fi
   * Heslo Wi-Fi
   * Připojovací řetězec zařízení

> [!Note]
> Přihlašovací údaje je uložen v EEPROM z Feather M0 WiFi. Pokud kliknete na tlačítko Obnovit na panel Feather M0 WiFi, ukázkovou aplikaci zeptá, jestli chcete vymazat informace. Zadejte `Y` vymazat informace. Budete vyzváni k zadání informací podruhé.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>Ověřte, že je úspěšně spuštěná ukázkové aplikace

Pokud se zobrazí následující výstup v okně sériového portu monitorování a blikající Indikátor na Feather M0 WiFi, ukázkové aplikace je úspěšně spuštěná:

![Finální výstup v rozhraním Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Další postup

Úspěšně připojeno ke službě IoT hub Feather M0 WiFi a data zachycená senzor odeslané do služby IoT hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

