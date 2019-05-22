---
title: Přidání skutečného zařízení do aplikace Azure IoT Central | Microsoft Docs
description: Jako operátor přidejte skutečné zařízení do aplikace Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: b073a270fabb8657be19b79601fa90f1b9a0351b
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "66015232"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Kurz: Přidání skutečného zařízení do aplikace Azure IoT Central

V tomto kurzu se naučíte, jak přidat skutečné zařízení do aplikace Microsoft Azure IoT Central a nakonfigurovat ho.

Tento kurz se skládá ze dvou částí:

1. Nejprve se jako operátor naučíte přidat skutečné zařízení do aplikace Azure IoT Central a nakonfigurovat ho. Na konci této části načtete připojovací řetězec, který použijete v druhé části.
2. Potom se jako vývojář zařízení dozvíte informace o kódu ve skutečném zařízení. Přidáte připojovací řetězec z první části do vzorového kódu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat nové skutečné zařízení
> * Nakonfigurovat skutečné zařízení
> * Získat připojovací řetězec pro skutečné zařízení z aplikace
> * Porozumět, jak se klientský kód mapuje k aplikaci
> * Nakonfigurovat klientský kód pro skutečné zařízení

## <a name="prerequisites"></a>Požadavky

Než se pustíte do práce, měl by tvůrce dokončit minimálně první kurz pro tvůrce a vytvořit aplikaci Azure IoT Central:

* [Definování nového typu zařízení](tutorial-define-device-type.md) (povinné)
* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md) (nepovinné)
* [Přizpůsobení zobrazení operátora](tutorial-customize-operator.md) (nepovinné)

Nainstalujte [Node.js](https://nodejs.org/) verze 8.0.0 nebo novějším na vývojovém počítači. Můžete spustit `node --version` na příkazovém řádku k ověření verze. Node.js je k dispozici pro širokou škálu operačních systémů.

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

Pokud chcete přidat skutečné zařízení do aplikace, použijete šablonu zařízení **Connected Air Conditioner** (Připojené klimatizační zařízení), kterou jste vytvořili v kurzu [Definování nového typu zařízení](tutorial-define-device-type.md).

1. Pokud chcete nové zařízení přidat jako operátor, zvolte v levé navigační nabídce možnost **Device Explorer**:

   ![Stránka Device Explorer zobrazující připojené klimatizační zařízení](media/tutorial-add-device/explorer.png)

   **Device Explorer** ukazuje **připojené Vzduchovod** zařízení šablon a simulovaných zařízení. Při vytváření šablony zařízení IoT Central automaticky vytvoří simulované zařízení.

2. Chcete-li začít připojovat vzduchovod reálné připojených zařízení, vyberte **+**, pak **skutečné**:

   ![Zahájení přidávání nového skutečného klimatizačního zařízení](media/tutorial-add-device/newreal.png)

3. Zadejte ID zařízení (by měl být malými písmeny) nebo použijte navrhovaný ID zařízení. Můžete také zadat název nového zařízení a zvolit **Create** (Vytvořit).

   ![Přejmenování zařízení](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Konfigurace skutečného zařízení

Skutečné zařízení se vytvoří ze šablony **Connected Air Conditioner**. Na stránce **Settings** (Nastavení) můžete nakonfigurovat zařízení a nastavit hodnoty vlastností pro záznam informací o zařízení.

1. Na stránce **Settings** si všimněte, že stav nastavení **Set temperature** (Nastavit teplotu) je **no update** (Bez aktualizace). Tento stav zůstává beze změny až do té doby, než se skutečné zařízení připojí k aplikaci a potvrdí, že na nastavení reaguje.

    ![Nastavení zobrazující synchronizaci](media/tutorial-add-device/settingssyncing.png)

2. Na **vlastnosti** stránku pro nové, skutečné zařízení, i umístění služby a datum posledního služby jsou upravitelné vlastnosti. Pole se sériovým číslem a verzí firmwaru budou prázdné, dokud se zařízení nepřipojí k aplikaci. Tyto hodnoty jen pro čtení se odesílají ze zařízení a nelze jej upravit.

    ![Vlastnosti skutečného zařízení](media/tutorial-add-device/setproperties1.png)

3. Pro své skutečné zařízení můžete zobrazit stránky **Measurements** (Měření), **Rules** (Pravidla) a **Dashboard** (Řídicí panel).

## <a name="generate-connection-string"></a>Generovat připojovací řetězec

Vývojář zařízení musí vložit *připojovací řetězec* pro skutečné zařízení do kódu, který běží na zařízení. Připojovací řetězec povoluje v zařízení se navázat zabezpečené připojení k vaší aplikaci. Následující kroky ukazují generovat připojovací řetězec a připravte klienta kód Node.js.

## <a name="prepare-the-client-code"></a>Příprava klientského kódu

Ukázkový kód v tomto článku je napsán v [Node.js](https://nodejs.org/) a ukazuje dostatek kódu:

* Připojit zařízení k aplikaci Azure IoT Central
* Odeslat teplotní telemetrii jako připojené klimatizační zařízení
* Odeslat vlastnosti zařízení do aplikace Azure IoT Central
* Odpovědět operátorovi, který používá nastavení **Set Temperature**
* Zpracovat příkaz Echo z aplikace Azure IoT Central

V článcích uvedených v [další kroky](#next-steps) části zahrnout kompletní ukázky a zobrazení jiných programovacích jazycích. Další informace o tom, jak se zařízení připojují k aplikaci Azure IoT Central, najdete v článku věnovaném [možnostem připojení zařízení](concepts-connectivity.md).

Následující postup popisuje, jak připravit ukázku [Node.js](https://nodejs.org/):

### <a name="get-the-device-connection-information"></a>Získání informací o připojení zařízení

1. Připojovací řetězec pro instanci zařízení ve vaší aplikaci se vygeneruje z informací o zařízení, které poskytuje IoT Central.

   Na obrazovce zařízení skutečné připojené klimatizace zvolte **Connect** (Připojit).

   ![Stránka zařízení s odkazem pro zobrazení informací o připojení](media/tutorial-add-device/connectionlink.png)

1. Na stránce připojení zařízení, poznamenejte si, **ID oboru**, **ID zařízení** a **primární klíč** hodnoty. Tyto hodnoty použijete v dalším kroku.

   ![Podrobnosti připojení](media/tutorial-add-device/device-connect.png)

### <a name="generate-the-connection-string"></a>Generovat připojovací řetězec

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

### <a name="prepare-the-nodejs-project"></a>Příprava projektu Node.js

1. Vytvořte složku s názvem `connectedairconditioner` na vývojovém počítači.

1. V prostředí příkazového řádku přejděte na složku `connectedairconditioner`, kterou jste vytvořili.

1. Pokud chcete inicializovat projekt Node.js, spusťte následující příkaz a přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
      ```

1. Potřebné balíčky nainstalujte spuštěním následujícího příkazu:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. V textovém editoru vytvořte soubor s názvem **ConnectedAirConditioner.js** ve složce `connectedairconditioner`.

1. Na začátek souboru **ConnectedAirConditioner.js** přidejte následující příkazy `require`:

    ```javascript
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Do souboru přidejte následující deklarace proměnných:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    > [!NOTE]
    > Zástupný symbol `{your device connection string}` aktualizujete později.

1. Uložte změny, které jste až dosud provedli, ale ponechejte soubor otevřený.

## <a name="review-client-code"></a>Revize kódu klienta

V předchozí části jste vytvořili kostru projektu Node.js pro aplikaci, která se připojuje k aplikaci Azure IoT Central. Dalším krokem je přidání kódu do:

* Připojit se k aplikaci Azure IoT Central
* Odeslat telemetrii do aplikace Azure IoT Central
* Odeslat vlastnosti zařízení do aplikace Azure IoT Central
* Přijmout nastavení z aplikace Azure IoT Central
* Zpracovat příkaz Echo z aplikace Azure IoT Central

1. Pokud chcete odeslat telemetrii do aplikace Azure IoT Central, přidejte následující kód do souboru **ConnectedAirConditioner.js**:

    ```javascript
    // Send device telemetry.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Název pole v JSON, který odesíláte, musí odpovídat názvu pole zadaného pro teplotní telemetrii v šabloně zařízení. V tomto příkladu se pole nazývá **temperature** (Teplota).

1. Abyste mohli odeslat vlastnosti zařízení, jako je **firmwareVersion** (verze firmwaru) a **serialNumber** (sériové číslo), přidejte následující definici:

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) => 
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. Pokud chcete definovat nastavení, které vaše zařízení podporuje, například **setTemperature**, přidejte následující definici:

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Abyste mohli zpracovat nastavení odeslaná z aplikace Azure IoT Central, přidejte následující funkci, která vyhledá a spustí příslušný kód zařízení:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

    Tato funkce:

    * sleduje odeslání požadované vlastnosti z aplikace Azure IoT Central,
    * vyhledá příslušnou funkci, která se má zavolat, aby se zpracovala změna nastavení,
    * odešle potvrzení zpět do aplikace Azure IoT Central.

1. Pokud chcete z aplikace Azure IoT Central reagovat na příkaz, jako je **echo**, přidejte následující definici:

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
    }
    ```

1. Přidejte následující kód, abyste dokončili připojení k aplikaci Azure IoT Central a připojili funkce v klientském kódu:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Setup device command callbacks
        client.onDeviceMethod('echo', onCommandEcho);
        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    client.open(connectCallback);
    ```

1. Uložte změny, které jste až dosud provedli, ale ponechejte soubor otevřený.

## <a name="configure-client-code"></a>Konfigurace klientského kódu

<!-- Add the connection string to the sample code, build, and run -->
Pokud chcete nakonfigurovat klientský kód na připojení k aplikaci Azure IoT Central, musíte přidat připojovací řetězec skutečného zařízení, který jste si poznamenali dříve v tomto kurzu.

1. V souboru **ConnectedAirConditioner.js** vyhledejte následující řádek kódu:

    ```javascript
    var connectionString = '{your device connection string}';
    ```

1. Nahraďte řetězec `{your device connection string}` připojovacím řetězcem skutečného zařízení. Jste zkopírovali připojovací řetězec, který jste vygenerovali v předchozím kroku.

1. Uložte změny v souboru **ConnectedAirConditioner.js**.

1. Ukázku spustíte tak, že v prostředí příkazového řádku spustíte následující příkaz:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Zkontrolujte, že se nacházíte ve složce `connectedairconditioner`, když spouštíte tento příkaz.

1. Aplikace zobrazí výstup do konzoly:

   ![Výstup klientské aplikace](media/tutorial-add-device/output.png)

1. Přibližně po 30 sekundách uvidíte telemetrii na stránce zařízení **Measurements**:

   ![Skutečné ~ ~ telemetrie](media/tutorial-add-device/realtelemetry.png)

1. Na stránce **Settings** uvidíte, že nastavení je nyní synchronizováno. Při prvním připojení zařízení obdrželo hodnotu nastavení a potvrdilo změnu:

   ![Nastavení synchronizováno](media/tutorial-add-device/settingsynced.png)

1. Na stránce **Settings** nastavte teplotu zařízení na **95** a zvolte **Update device** (Aktualizovat zařízení). Ukázková aplikace obdrží tuto změnu a zpracuje ji:

   ![Příjem a zpracování nastavení](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Existují dvě zprávy týkající se aktualizace nastavení. Jedna zpráva se používá při odeslání stavu `pending` a druhá při odeslání stavu `completed`.

1. Na stránce **Measurements** si můžete všimnout, že zařízení posílá vyšší teplotní hodnoty:

    ![Teplotní telemetrie je nyní vyšší.](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="nextstepaction"]
> * Přidat nové skutečné zařízení
> * Nakonfigurovat nové zařízení
> * Získat připojovací řetězec pro skutečné zařízení z aplikace
> * Porozumět, jak se klientský kód mapuje k aplikaci
> * Nakonfigurovat klientský kód pro skutečné zařízení

Teď, když jste se připojili skutečné zařízení do aplikace Azure IoT Central, tady jsou některé další navrhované kroky.

Jako operátor se naučíte tyto postupy:

* [Správa zařízení](howto-manage-devices.md)
* [Použití sad zařízení](howto-use-device-sets.md)
* [Vytvoření vlastní analýzy](howto-use-device-sets.md)

Jako vývojář zařízení se naučíte tyto postupy:

* [Příprava a připojení zařízení DevKit (C)](howto-connect-devkit.md)
* [Příprava a připojte Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Příprava a připojte Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Příprava a připojit zařízení s Windows 10 IoT core (C#)](howto-connect-windowsiotcore.md)
* [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs.md)