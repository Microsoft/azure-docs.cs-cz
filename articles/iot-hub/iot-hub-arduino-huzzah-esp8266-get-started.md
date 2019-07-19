---
title: ESP8266 do cloudu – připojení HUZZAH ESP8266 do Azure IoT Hub | Microsoft Docs
description: Naučte se, jak nastavit a propojit Adafruité peří HUZZAH ESP8266 do Azure IoT Hub, aby odesílaly data na cloudovou platformu Azure v tomto kurzu.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 8e97a979c37af8ade51b4ff6ca4b2c5b4eec126e
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68232720"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Propojení Adafruitch peří HUZZAH ESP8266 do Azure IoT Hub v cloudu

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Připojení mezi DHT22, peří HUZZAH ESP8266 a IoT Hub](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Co dělat

Připojte Adafruit peří HUZZAH ESP8266 do služby IoT Hub, kterou vytvoříte. Pak spustíte ukázkovou aplikaci na ESP8266 a shromáždíte data o teplotě a vlhkosti ze senzoru DHT22. Nakonec odešlete data ze senzorů do služby IoT Hub.

> [!NOTE]
> Pokud používáte jiné desky ESP8266, můžete je pomocí těchto kroků připojit ke službě IoT Hub. V závislosti na ESP8266 vývěsce, kterou používáte, možná budete muset změnit konfiguraci `LED_PIN`. Například pokud používáte ESP8266 z AI, můžete ho změnit z `0` na. `2` Ještě nemáte k dispozici sadu? Získejte ho z [webu Azure](https://azure.com/iotstarterkits).

## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby IoT Hub a registrace zařízení pro HUZZAH ESP8266
* Jak propojit peří HUZZAH ESP8266 se senzorem a počítačem
* Jak shromažďovat data ze senzorů spuštěním ukázkové aplikace na prolnutích HUZZAH ESP8266
* Jak odesílat data ze senzorů do služby IoT Hub

## <a name="what-you-need"></a>Co potřebujete

![Části potřebné pro tento kurz](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

K dokončení této operace budete potřebovat následující části z HUZZAHu ESP8266 Starter sady pro prolnutí:

* Panel HUZZAH okrajů ESP8266
* Micro USB pro typ kabelu USB

Pro vaše vývojové prostředí potřebujete také následující věci:

* Aktivní předplatné Azure. Pokud účet Azure nemáte, [Vytvořte si bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) během několika minut.
* Počítač Mac nebo počítač se systémem Windows nebo Ubuntu.
* [GIT](https://git-scm.com/download)
* [Arduino](https://www.arduino.cc/en/main/software#download)
* [Ovladač ESP8266 Adafruit Huzzah pro USB](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide)
* Bezdrátová síť pro prolnutí HUZZAH ESP8266, ke které se chcete připojit.
* Připojení k Internetu ke stažení konfiguračního nástroje.
* [Visual Studio Code rozšíření pro Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> Verze Arduino IDE používaná rozšířením Visual Studio Code pro Arduino musí být verze 1.6.8 nebo novější. Starší verze nefungují s knihovnou AzureIoT.

Následující položky jsou volitelné pro případ, že nemáte senzor. Máte také možnost použít Simulovaná data senzorů.

* Teplota DHT22 a snímač vlhkosti Adafruit
* Breadboard
* Dráty můstků m/M

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Propojení peří HUZZAH ESP8266 se senzorem a počítačem

V této části propojíte senzory s vaší deskou. Pak zařízení připojíte k počítači, abyste ho mohli dále používat.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Připojení teploty DHT22 a snímače vlhkosti k prolnutí HUZZAH ESP8266

Pomocí Breadboard a vodičů můstků vytvořte připojení následujícím způsobem. Pokud nemáte senzor, přeskočte tuto část, protože místo toho můžete použít Simulovaná data snímače.

![Odkaz na připojení](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

Pro PIN kódy senzorů použijte následující kabely:

| Start (senzor)           | Konec (panel)            | Barva kabelu   |
| -----------------------  | ---------------------- | ------------  |
| VDD (Pin 31F)            | 3V (připnout 58H)           | Červený kabel     |
| DATA (Pin 32F)           | GPIO 2 (Pin 46A)       | Modrý kabel    |
| GND (připnout 34F)            | GND (připnout 56I)          | Černý kabel   |

Další informace najdete v tématu [Nastavení senzoru ADAFRUIT DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) a [Adafruit peří HUZZAH Esp8266 pinouts](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).

Teď by se měly Huzzahovat ESP8266y, které by se měly připojit k pracovnímu senzoru.

![Připojení DHT22 s Huzzahými peří](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Připojení peří HUZZAH ESP8266 k vašemu počítači

Jak je uvedeno dále, pomocí mikrosběrnice USB zadejte kabel USB pro připojení peří HUZZAH ESP8266 k vašemu počítači.

![Připojení Huzzah k počítači přes peří](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Přidat oprávnění sériového portu (jenom Ubuntu)

Pokud používáte Ubuntu, ujistěte se, že máte oprávnění k provozu na portu USB peří HUZZAH ESP8266. Pokud chcete přidat oprávnění sériového portu, postupujte podle těchto kroků:

1. V terminálu spusťte následující příkazy:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Získáte jeden z následujících výstupů:

   * CRW-RW----1 root UUCP xxxxxxxx
   * CRW-RW----1 root Dialing xxxxxxxx

   Ve výstupu si všimněte, že `uucp` nebo `dialout` se jedná o název vlastníka skupiny portu USB.

2. Přidejte uživatele do skupiny spuštěním následujícího příkazu:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>`je název vlastníka skupiny, který jste získali v předchozím kroku. `<username>`je vaše uživatelské jméno Ubuntu.

3. Odhlaste se z Ubuntu a potom se znovu přihlaste, aby se změna zobrazila.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Shromáždění dat ze senzorů a jejich odeslání do služby IoT Hub

V této části nasadíte a spustíte ukázkovou aplikaci na prolnutích HUZZAH ESP8266. Ukázková aplikace zabliká indikátor LED na prostředcích HUZZAH ESP8266 a pošle data o teplotě a vlhkosti shromážděná ze senzoru DHT22 do služby IoT Hub.

### <a name="get-the-sample-application-from-github"></a>Získat ukázkovou aplikaci z GitHubu

Ukázková aplikace je hostovaná na GitHubu. Naklonujte ukázkové úložiště obsahující ukázkovou aplikaci z GitHubu. K naklonování ukázkového úložiště použijte následující postup:

1. Otevřete příkazový řádek nebo okno terminálu.

2. Přejít do složky, kam chcete uložit ukázkovou aplikaci.

3. Spusťte následující příkaz:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   V dalším kroku nainstalujte balíček pro peří HUZZAH ESP8266 v Visual Studio Code.

4. Otevřete složku, ve které je uložena ukázková aplikace.

5. Otevřete soubor App. ino ve složce aplikace v Visual Studio Code.

   ![Otevřete ukázkovou aplikaci v Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. Do Visual Studio Code zadejte `F1`.

7. Zadejte **Arduino** a vyberte **Arduino: Správce**panelu.

8. Na kartě **správce panelu Arduino** klikněte na **další adresy URL**.

   ![VS Code správce panelu Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. V okně **uživatelská nastavení** zkopírujte a vložte následující na konec souboru.

   ```json
   "arduino.additionalUrls": "https://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![Konfigurace adresy URL balíčku Arduino v VS Code](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. Uložte soubor a zavřete kartu **uživatelská nastavení** .

11. Klikněte na **aktualizovat indexy balíčků**. Po dokončení aktualizace vyhledejte **ESP8266**.

12. Klikněte na tlačítko **nainstalovat** pro ESP8266.

    Správce desek indikuje, že je nainstalovaná ESP8266 s verzí 2.2.0 nebo novější.

    ![Balíček ESP8266 je nainstalovaný.](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. Zadejte `F1`, zadejte **Arduino** a vyberte **Arduino: Konfigurace**panelu

14. Klikněte na box pro **vybranou šachovnici:** a zadejte **ESP8266**a pak vyberte **Adafruit HUZZAH ESP8266 (ESP8266)** .

    ![Vybrat panel ESP8266](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Nainstalovat potřebné knihovny

1. Do Visual Studio Code zadejte `F1`a pak zadejte **Arduino** a vyberte **Arduino: Správce**knihovny.

2. Vyhledejte následující názvy knihoven jednu po jedné. U každé knihovny, kterou najdete, klikněte na **nainstalovat**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nemáte skutečný senzor DHT22?

Ukázková aplikace může simulovat data o teplotě a vlhkosti pro případ, že nemáte skutečný DHT22 senzor. Chcete-li nastavit ukázkovou aplikaci pro použití simulovaných dat, postupujte podle následujících kroků:

1. `config.h` Otevřete soubor`app` ve složce.

2. Vyhledejte následující řádek kódu a změňte hodnotu z `false` na: `true`

   ```c
   define SIMULATED_DATA true
   ```

   ![Konfigurace ukázkové aplikace pro použití simulovaných dat](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. Uložte soubor.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Nasazení ukázkové aplikace pro prolnutí HUZZAH ESP8266

1. V Visual Studio Code na stavovém řádku klikněte na  **\<vybrat > sériového portu** a potom klikněte na sériový port pro prolnutí HUZZAH ESP8266.

2. Zadejte `F1`, zadejte **Arduino** a vyberte **Arduino: Nahrajte** sestavení a nasaďte ukázkovou aplikaci, která bude používat HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Zadejte svoje přihlašovací údaje.

Po úspěšném dokončení nahrávání pomocí těchto kroků zadejte svoje přihlašovací údaje:

1. Otevřete Arduino IDE, klikněte na **nástroje** > **sériové monitorování**.

2. V okně sledování sériového čísla si všimněte obou rozevíracích seznamů v pravém dolním rohu.

3. Vyberte pro levý rozevírací seznam položku **žádný konec řádku** .

4. U pravého rozevíracího seznamu vyberte **115200 baud** .

5. Do vstupního pole v horní části okna sledování sériového portu zadejte následující informace, pokud jste vyzváni k jejich zadání, a poté klikněte na tlačítko **Odeslat**.

   * Identifikátor SSID sítě Wi-Fi
   * Heslo Wi-Fi
   * Připojovací řetězec zařízení

> [!Note]
> Přihlašovací údaje se ukládají do paměti EEPROM HUZZAH ESP8266. Pokud kliknete na tlačítko obnovit na panelu peří HUZZAH ESP8266, ukázková aplikace se zeptá, jestli chcete informace vymazat. Zadejte `Y` , aby se informace vymazaly. Zobrazí se výzva k zadání informací podruhé.

### <a name="verify-the-sample-application-is-running-successfully"></a>Ověřte, že je ukázková aplikace úspěšně spuštěná.

Pokud se zobrazí následující výstup z okna sledování sériového portu a indikátor blikání na peří HUZZAH ESP8266, ukázková aplikace je úspěšně spuštěná.

![Konečný výstup v Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Přečtěte si zprávy přijaté vaším rozbočovačem.

Jedním ze způsobů, jak monitorovat zprávy přijaté službou IoT Hub ze svého zařízení, je používat nástroje Azure IoT pro Visual Studio Code. Další informace najdete v tématu [použití nástrojů Azure IoT pro Visual Studio Code k posílání a přijímání zpráv mezi zařízením a IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Další způsoby, jak zpracovávat data odesílaná vaším zařízením, můžete pokračovat k další části.

## <a name="next-steps"></a>Další postup

Úspěšně jste provedli propojení HUZZAHch peří k vašemu centru IoT a data zaznamenaného snímače se odeslala do služby IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]