---
title: M0 do cloudu--prolnutí M0 Wi-Fi připojení ke službě Azure IoT Hub | Microsoft Docs
description: Zjistěte, jak nastavit a Adafruit prolnutí M0 Wi-Fi připojení ke službě Azure IoT Hub k odesílání dat do Azure Cloudová platforma v tomto kurzu.
author: rangv
manager: nasing
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 2a6899bbd294a16dee3a767e976a92deaa00f0e2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631866"
---
# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Připojení ke službě Azure IoT Hub v cloudu Adafruit prolnutí M0 Wi-Fi
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Připojení mezi BME280, prolnutí M0 Wi-Fi a IoT Hub](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

V tomto kurzu zahájíte učení základní informace o práci s Arduino panel. Pak zjistíte, jak bezproblémově připojení zařízení do cloudu pomocí [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>Co dělat

Připojte Adafruit prolnutí M0 Wi-Fi do služby IoT hub, který vytvoříte. Pak spusťte ukázkovou aplikaci na M0 Wi-Fi ke shromažďování dat teploty a vlhkosti z BME280. Nakonec odeslat data snímačů do služby IoT hub.


## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby IoT hub a registrovat zařízení pro prolnutí M0 Wi-Fi
* Postup připojení Wi-Fi M0 prolnutí s senzoru a počítač
* Postup shromažďování dat snímačů spuštěním ukázkovou aplikaci na prolnutí M0 Wi-Fi
* Postup odesílání dat snímačů do služby IoT hub

## <a name="what-you-need"></a>Co potřebujete

![součásti potřebné pro tento kurz](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

Pro dokončení této operace, musíte z vaší prolnutí M0 Wi-Fi Starter Kit následujících částí:

* Panel prolnutí M0 Wi-Fi
* Micro USB na kabelu USB typ A

Vývojové prostředí musíte taky následujících akcí:

* Aktivní předplatné Azure. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Mac nebo počítači se systémem Windows nebo Ubuntu.
* Bezdrátové sítě Wi-Fi M0 prolnutí pro připojení k.
* Připojení k Internetu kvůli stahování nástroj konfigurace.
* [Arduino IDE](https://www.arduino.cc/en/main/software) verze 1.6.8 nebo novější. Starší verze nepodporují s knihovnou Azure IoT Hub.

Pokud nemáte senzoru, následující položky jsou volitelné. Máte také možnost používat data simulované snímače:

* Senzor teploty a vlhkosti BME280
* Breadboard
* Vodičům můstek M/M

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Připojení Wi-Fi M0 prolnutí s senzoru a počítač
V této části se připojíte k vaší karty snímače. Potom můžete připojení tohoto zařízení do počítače pro další použití.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Senzor teploty a vlhkosti DHT22 připojit k prolnutí M0 Wi-Fi

Pomocí vedení breadboard a můstek pro připojení. Pokud nemáte senzoru, tuto část přeskočte, protože data snímačů simulované můžete použít místo.

![odkaz na připojení](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Senzor kód PIN použijte následující kabeláž:


| Spuštění (senzor)           | End (panelu)            | Kabel barev   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27A)            | 3v (3A PIN kódu)            | Red kabel     |
| ZEM (Pin 29A)            | ZEM (6A PIN kódu)           | Začernit kabel   |
| SCK (Pin 30A)            | SCK (Pin 12A)          | Žlutý kabel  |
| SDO (Pin 31A)            | MI (Pin 14A)           | Bílé kabel   |
| SDI (Pin 32A)            | M0 (Pin 13A)           | Modrý kabel    |
| CS (Pin 33A)             | GPIO 5 (Pin 15J)       | Oranžové kabel  |

Další informace najdete v tématu [Adafruit BME280 vlhkosti + barometrický tlak + teplotní snímač volnými](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) a [uspořádání kolíků Adafruit prolnutí M0 Wi-Fi](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Nyní by měly být připojené vaší prolnutí M0 Wi-Fi s pracovní senzoru.

![Spojte se s prolnutí Huzzah DHT22](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Připojte k počítači prolnutí M0 Wi-Fi

Pro připojení Wi-Fi M0 prolnutí do počítače, použijte Micro USB na kabelu USB typ A jak je znázorněno:

![Prolnutí Huzzah připojte k počítači](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Přidání oprávnění sériového portu (pouze Ubuntu)

Pokud používáte Ubuntu, zkontrolujte, zda že máte oprávnění k provozu na USB port z prolnutí M0 Wi-Fi. Pokud chcete přidat oprávnění sériového portu, postupujte takto:


1. V terminálu spusťte následující příkazy:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Zobrazí jednu z následujících výstupy:

   * CRW-rw---1 kořenové uucp xxxxxxxx
   * CRW-rw---xxxxxxxx síti službou 1 root

   Ve výstupu, Všimněte si, že `uucp` nebo `dialout` je název skupiny vlastníka portu USB.

2. Přidejte uživatele do skupiny, spusťte následující příkaz:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   V předchozím kroku jste získali název vlastníka skupiny `<group-owner-name>`. Uživatelské jméno Ubuntu `<username>`.

3. Změna zobrazí, odhlaste se od Ubuntu a potom se znovu přihlaste.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Shromažďování dat snímačů a odeslat do služby IoT hub

V této části nasazení a spuštění ukázkové aplikace na prolnutí M0 Wi-Fi. Ukázkové aplikace vytváří blikání DIODU na prolnutí M0 Wi-Fi. Pak odešle teploty a vlhkosti data shromážděná z senzoru BME280 do služby IoT hub.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>Získat ukázkovou aplikaci z webu GitHub a příprava Arduino IDE

Ukázkové aplikace jsou hostované na Githubu. Naklonujte úložiště ukázka, která obsahuje ukázkovou aplikaci z webu GitHub. Klonovat úložiště ukázkové, postupujte takto:

1. Otevřete příkazový řádek nebo okno terminálu.

2. Přejděte do složky, kam chcete ukázkovou aplikaci k uložení.
3. Spusťte následující příkaz:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>Instalace balíčku pro prolnutí M0 Wi-Fi v Arduino IDE

1. Otevřete složku, kde je uložen ukázkovou aplikaci.

2. Otevřete soubor app.ino ve složce aplikace v prostředí IDE Arduino.

   ![Otevřete ukázkovou aplikaci v Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. Klikněte na tlačítko **soubor** > **Předvolby** (Windows nebo Linuxem) nebo **Arduino** > **Předvolby** (Mac) a zkopírujte a vložte odkaz do níže **další adresy URL Manager panely** možnost v předvolbách Arduino IDE.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. Klikněte na tlačítko **nástroje** > **Tabule** > **Manager panely**a pak nainstalujte `Arduino SAMD Boards` verze `1.6.2` nebo novější. 

1. V rámci stejného časového období, nainstalujte `Adafruit SAMD Boards` balíček, který chcete přidat soubor definice panelu.

   ![instalaci balíčku esp8266](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. Klikněte na tlačítko **nástroje** > **Tabule** > **Adafruit M0 Wi-Fi**.

5. Instalace ovladačů (jenom Windows). Po připojení Wi-Fi M0 prolnutí, možná muset nainstalovat ovladač. Klikněte na tlačítko [odkaz ke stažení na webové stránce](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) pro stažení instalačního programu ovladače. Postupujte podle kroků k instalaci ovladačů, které chcete.

### <a name="install-necessary-libraries"></a>Instalace nezbytné knihovny

1. V prostředí IDE Arduino, klikněte na tlačítko **nákresu** > **zahrnují knihovny** > **spravovat knihovny**.

2. Vyhledejte následující knihovny názvy po jednom. Pro každou knihovnu, která zjistíte, klikněte na tlačítko **nainstalovat**:

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. Nainstalujte ručně `Adafruit_WINC1500`. Přejděte na [tento web](https://github.com/adafruit/Adafruit_WINC1500) a klikněte na tlačítko **klonovat nebo stáhnout** > **stáhnout ZIP**. Potom vaší Arduino IDE, přejděte na **nákresu** > **zahrnují knihovny** > **přidat .zip knihovna** a přidejte soubor zip.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>Pokud nemáte skutečné senzor BME280 použít ukázkové aplikace

Pokud nemáte skutečné senzor BME280, ukázkové aplikace můžete simulovat teploty a vlhkosti data. Pokud chcete nastavit ukázkovou aplikaci používat simulované dat, postupujte takto:

1. Otevřete `config.h` v soubor `app` složky.

2. Vyhledejte následující řádek kódu a změňte hodnotu z `false` k `true`:

   ```c
   define SIMULATED_DATA true
   ```
   ![Nakonfigurujte ukázkovou aplikaci používat simulované dat](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. Uložte soubor s `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Nasadit ukázkovou aplikaci pro prolnutí M0 Wi-Fi

1. V prostředí IDE Arduino, klikněte na tlačítko **nástroj** > **Port**a potom klikněte na sériového portu pro prolnutí M0 Wi-Fi.

2. Klikněte na tlačítko **nákresu** > **nahrát** k vytváření a nasazování ukázkovou aplikaci pro prolnutí M0 Wi-Fi.

### <a name="enter-your-credentials"></a>Zadejte přihlašovací údaje.

Po úspěšném dokončení nahrávání, zadejte své přihlašovací údaje takto:

1. V prostředí IDE Arduino, klikněte na tlačítko **nástroje** > **sériové monitorování**.

2. V pravém dolním rohu okna sériové monitorování, vyberte **žádný řádek ukončování** v rozevíracím seznamu na levé straně.
3. Vyberte **115200 přenosová** v rozevíracím seznamu na pravé straně.
4. Do vstupního pole v horní části, zadejte následující informace, pokud budete vyzváni k tomu a klikněte na tlačítko **odeslat**:

   * SSID sítě Wi-Fi
   * Heslo Wi-Fi
   * Řetězec připojení zařízení

> [!Note]
> Informace o přihlašovacích údajích uložený v EEPROM z prolnutí M0 WiFi. Pokud kliknete na tlačítko Obnovit na panelu prolnutí M0 Wi-Fi, ukázková aplikace zobrazí, pokud chcete vymazat informace. Zadejte `Y` vymazat informace. Budete vyzváni k zadání informací ještě jednou.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>Ověřte, zda je ukázková aplikace úspěšně spuštěna

Pokud se zobrazí následující výstup z okna sériové monitorování a blikající LED na prolnutí M0 Wi-Fi, je ukázka aplikace spuštěna úspěšně:

![Závěrečný výstup v integrovaném vývojovém prostředí Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Další postup

Úspěšně jste připojení Wi-Fi M0 prolnutí do služby IoT hub a data zaznamenaná senzor odeslané do služby IoT hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

