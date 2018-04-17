---
title: ESP8266 do cloudu - připojit ke službě Azure IoT Hub prolnutí HUZZAH ESP8266 | Microsoft Docs
description: Zjistěte, jak nastavit a Adafruit prolnutí HUZZAH ESP8266 připojit ke službě Azure IoT Hub pro něj k odesílání dat do Azure Cloudová platforma v tomto kurzu.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: ''
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: b850bb25d7a6d06a2d0f55ed915321badf76bb39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Adafruit prolnutí HUZZAH ESP8266 připojit ke službě Azure IoT Hub v cloudu

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Připojení mezi DHT22, prolnutí HUZZAH ESP8266 a IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Co dělat


Připojte Adafruit prolnutí HUZZAH ESP8266 do služby IoT hub, který vytvoříte. Pak spusťte ukázkovou aplikaci na ESP8266 ke shromažďování dat teploty a vlhkosti ze DHT22 senzoru. Nakonec odeslat data snímačů do služby IoT hub.

> [!NOTE]
> Pokud používáte jiné ESP8266 panely, můžete stále následujícím postupem pro připojení do služby IoT hub. V závislosti na ESP8266 panelu, který používáte, možná budete muset překonfigurovat `LED_PIN`. Například pokud používáte ESP8266 z AI Thinker, můžete změnit z `0` k `2`. Nemáte sady ještě? Získat z [webu Azure](http://azure.com/iotstarterkits).




## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby IoT hub a registrovat zařízení pro prolnutí HUZZAH ESP8266
* Postup připojení prolnutí HUZZAH ESP8266 s senzoru a počítač
* Postup shromažďování dat snímačů spuštěním ukázkovou aplikaci na prolnutí HUZZAH ESP8266
* Postup odesílání dat snímačů do služby IoT hub

## <a name="what-you-need"></a>Co potřebujete

![součásti potřebné pro tento kurz](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Pro dokončení této operace, musíte z vaší prolnutí HUZZAH ESP8266 Starter Kit následujících částí:

* Prolnutí HUZZAH ESP8266 panelu
* Micro USB na kabelu USB typ A

Vývojové prostředí musíte taky následujících akcí:

* Aktivní předplatné Azure. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Mac nebo počítači se systémem Windows nebo Ubuntu.
* Bezdrátové sítě pro prolnutí HUZZAH ESP8266 pro připojení k.
* Připojení k Internetu stahovat nástroj konfigurace.
* [Rozšíření sady Visual Studio Code pro Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> Verze Arduino IDE pro Arduino musí být verze 1.6.8 používá rozšíření Visual Studio Code nebo novější. Starší verze nepodporují s knihovnou AzureIoT.

Následující položky jsou volitelné, v případě, že nemáte senzoru. Máte také možnost používat data snímačů simulované.

* Senzor teploty a vlhkosti Adafruit DHT22
* Breadboard
* Vodičům můstek M/M


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Připojit prolnutí HUZZAH ESP8266 s senzoru a počítače
V této části se připojíte k vaší karty snímače. Potom můžete připojení tohoto zařízení do počítače pro další použití.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Senzor teploty a vlhkosti DHT22 připojit k prolnutí HUZZAH ESP8266

Pomocí vedení breadboard a můstek k připojení následujícím způsobem. Pokud nemáte senzoru, tuto část přeskočte, protože data snímačů simulované můžete použít místo.

![odkaz na připojení](media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)


Senzor kód PIN použijte následující kabeláž:


| Spuštění (senzor)           | End (panelu)           | Kabel barev   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 31F)            | 3v (připnout 58H)           | Red kabel     |
| DATA (Pin 32F)           | GPIO 2 (Pin 46A)       | Modrý kabel    |
| ZEM (Pin 34F)            | ZEM (PIn 56I)          | Začernit kabel   |

Další informace najdete v tématu [Adafruit DHT22 senzor instalace](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) a [uspořádání Adafruit prolnutí HUZZAH Esp8266 kolíků](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).



Teď vaše ESP8266 Huzzah prolnutí by měly být připojené s pracovní senzoru.

![Spojte se s prolnutí Huzzah DHT22](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Prolnutí HUZZAH ESP8266 připojte k počítači

Jak ukazuje následující, prolnutí HUZZAH ESP8266 připojte k počítači pomocí USB Micro kabelem USB typ A.

![Prolnutí Huzzah připojte k počítači](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Přidání oprávnění sériového portu (pouze Ubuntu)


Pokud používáte Ubuntu, zkontrolujte, zda že máte oprávnění k provozu na USB port z prolnutí HUZZAH ESP8266. Pokud chcete přidat oprávnění sériového portu, postupujte takto:


1. V terminálu, spusťte následující příkazy:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Zobrazí jednu z následujících výstupy:

   * CRW-rw---1 kořenové uucp xxxxxxxx
   * CRW-rw---xxxxxxxx síti službou 1 root

   Ve výstupu, Všimněte si, že `uucp` nebo `dialout` je název skupiny vlastníka portu USB.

1. Přidejte uživatele do skupiny tak, že spustíte následující příkaz:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` je název vlastníka skupiny, kterou jste získali v předchozím kroku. `<username>` je Ubuntu uživatelské jméno.

1. Odhlaste se od Ubuntu a znovu se přihlaste změna zobrazí.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Shromažďování dat snímačů a odeslat do služby IoT hub

V této části nasazení a spuštění ukázkové aplikace na prolnutí HUZZAH ESP8266. Ukázkovou aplikaci bliká DIODU na prolnutí HUZZAH ESP8266 a odešle teploty a vlhkosti data shromážděná z senzoru DHT22 do služby IoT hub.

### <a name="get-the-sample-application-from-github"></a>Získat ukázkovou aplikaci z webu GitHub

Ukázkové aplikace jsou hostované na Githubu. Naklonujte úložiště ukázka, která obsahuje ukázkovou aplikaci z webu GitHub. Klonovat úložiště ukázkové, postupujte takto:

1. Otevřete příkazový řádek nebo okno terminálu.
1. Přejděte do složky, kam chcete ukázkovou aplikaci k uložení.
1. Spusťte následující příkaz:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Instalace balíčku pro prolnutí HUZZAH ESP8266 ve Visual Studio Code:

1. Otevřete složku, kde je uložen ukázkovou aplikaci.
1. Otevřete soubor app.ino ve složce aplikace ve Visual Studio Code.

   ![Otevřete ukázkovou aplikaci v kódu Visual Studio](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

1. Visual Studio Code, zadejte `F1`.
1. Typ **Arduino** a vyberte **Arduino: Tabule Manager**.
1. V **Arduino Tabule Manager** , klikněte na **další adresy URL**.

   ![VS Code Arduino panelu Správce](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

1. V **uživatelská nastavení** okno, zkopírujte a vložte následující na konci souboru

   `"arduino.additionalUrls": "http://arduino.esp8266.com/stable/package_esp8266com_index.json"`

   ![Konfigurovat adresu URL balíčku Arduino v produktu VS Code](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

1. Soubor uložte a zavřete **uživatelská nastavení** kartě.
1. Klikněte na tlačítko **aktualizovat balíček indexy**. Po dokončení aktualizace vyhledejte **esp8266**.
1. Klikněte na tlačítko **nainstalovat** tlačítko esp8266.

   Panely Manager znamená, že je nainstalována ESP8266 verzí 2.2.0 nebo novější.

   ![instalaci balíčku esp8266](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

1. Zadejte `F1`, pak zadejte **Arduino** a vyberte **Arduino: Konfigurace Tabule**.
1. Klikněte na pole pro **vybrané Tabule:** a typ **esp8266**, pak vyberte **Adafruit HUZZAH ESP8266 (esp8266)**.

   ![Vyberte esp8266 panelu](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Instalace nezbytné knihovny

1. Visual Studio Code, zadejte `F1`, pak zadejte **Arduino** a vyberte **Arduino: Správce knihovny**.
1. Vyhledejte následující knihovny názvy po jednom. Pro každou knihovnu, která zjistíte, klikněte na tlačítko **nainstalovat**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nemáte skutečné senzor DHT22?

Ukázkové aplikace můžete simulovat teploty a vlhkosti dat v případě, že nemáte skutečné DHT22 senzoru. Pokud chcete nastavit ukázkovou aplikaci používat simulované dat, postupujte takto:

1. Otevřete `config.h` v soubor `app` složky.
1. Vyhledejte následující řádek kódu a změňte hodnotu z `false` k `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Nakonfigurujte ukázkovou aplikaci používat simulované dat](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

1. Uložte soubor.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Nasadit ukázkovou aplikaci pro prolnutí HUZZAH ESP8266

1. V kódu Visual Studio, klikněte na tlačítko **<Select Serial Port>** stav panel a potom klikněte na sériového portu pro prolnutí HUZZAH ESP8266.
1. Zadejte `F1`, pak zadejte **Arduino** a vyberte **Arduino: nahrát** k vytváření a nasazování ukázkovou aplikaci pro prolnutí HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Zadejte přihlašovací údaje.

Po úspěšném dokončení nahrávání, zadejte své přihlašovací údaje takto:

1. Otevřete Arduino IDE, klikněte na **nástroje** > **sériové monitorování**.
1. V okně serial sledování Všimněte si dvě rozevíracích seznamů v pravém dolním rohu.
1. Vyberte **žádný řádek ukončování** pro levý rozevíracího seznamu.
1. Vyberte **115200 přenosová** pro právo rozevíracího seznamu.
1. Do vstupního pole umístěné v horní části okna sériové sledování, zadejte následující informace, pokud se zobrazí výzva k poskytování je a pak klikněte na tlačítko **odeslat**.
   * SSID sítě Wi-Fi
   * Heslo Wi-Fi
   * Řetězec připojení zařízení

> [!Note]
> Informace o přihlašovacích údajích uložený v ESP8266 EEPROM z prolnutí HUZZAH. Pokud kliknete na tlačítko Obnovit na panelu prolnutí HUZZAH ESP8266, ukázkové aplikace zobrazí, pokud chcete vymazat informace. Zadejte `Y` k dispozici informace vymazat. Jste vyzváni k zadání informací ještě jednou.

### <a name="verify-the-sample-application-is-running-successfully"></a>Zkontrolujte, zda že ukázkové aplikace je úspěšně spuštěna.

Pokud se zobrazí následující výstup z okna sériové monitorování a blikající LED na prolnutí HUZZAH ESP8266, ukázkové aplikace je úspěšně spuštěna.

![Závěrečný výstup v integrovaném vývojovém prostředí Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="next-steps"></a>Další postup

Máte úspěšně připojen ESP8266 HUZZAH prolnutí do služby IoT hub a data zaznamenaná senzor odeslané do služby IoT hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

