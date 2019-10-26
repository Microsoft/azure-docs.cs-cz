---
title: Přidání skutečného zařízení do aplikace Azure IoT Central | Microsoft Docs
description: Jako operátor přidejte skutečné zařízení do aplikace Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 08/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 1f7afde7a5c4f3553cde40e4b179d056b8caee9b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72956205"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Kurz: Přidání skutečného zařízení do aplikace Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

V tomto kurzu se dozvíte, jak do IoT Central aplikace Microsoft Azure přidat a nakonfigurovat *reálné zařízení* . V tomto kurzu navedete kód reálného zařízení pomocí Node. js a spustíte kód na stolním počítači. K dokončení tohoto kurzu nepotřebujete samostatné zařízení IoT, jako je například zařízení malin PI nebo MXChip IoT DevKit.

Tento kurz se skládá ze dvou částí:

* Nejprve se jako operátor naučíte přidat skutečné zařízení do aplikace Azure IoT Central a nakonfigurovat ho. Na konci této části načtete připojovací řetězec, který použijete v druhé části.
* Potom se jako vývojář zařízení dozvíte informace o kódu ve skutečném zařízení. Přidáte připojovací řetězec z první části do vzorového kódu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat nové skutečné zařízení
> * Nakonfigurovat skutečné zařízení
> * Získat připojovací řetězec pro skutečné zařízení z aplikace
> * Porozumět, jak se klientský kód mapuje k aplikaci
> * Konfigurace klientského kódu pro skutečné zařízení

## <a name="prerequisites"></a>Předpoklady

Předtím, než začnete, je potřeba, aby tvůrce vytvořil aplikaci Azure IoT Central v prvním kurzu sestavování: [Definujte nový typ zařízení](tutorial-define-device-type.md) (povinné).

Nainstalujte [Node. js](https://nodejs.org/) verze 8.0.0 nebo novější na svém vývojovém počítači. Můžete spustit `node --version` na příkazovém řádku a ověřit svou verzi. Node.js je k dispozici pro širokou škálu operačních systémů.

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

Pokud chcete přidat skutečné zařízení do aplikace, použijete šablonu zařízení **Connected Air Conditioner** (Připojené klimatizační zařízení), kterou jste vytvořili v kurzu [Definování nového typu zařízení](tutorial-define-device-type.md).

1. Pokud chcete přidat nové zařízení jako operátor v levém podokně vyberte **zařízení** :

   ![Stránka Device Explorer zobrazující připojené klimatizační zařízení](media/tutorial-add-device/explorer.png)

   **Device Explorer** zobrazuje **připojenou** šablonu zařízení klimatizace pro klimatizace a simulované zařízení. Když vytvoříte šablonu zařízení, IoT Central automaticky vytvoří simulované zařízení.

2. Všimněte si, že **připojená** šablona zařízení klimatizace klimatizace je ta, kterou jste vybrali v **Device Explorer**. Pokud chcete začít s připojením reálného zařízení klimatizace, které tuto šablonu používá, vyberte **+** a potom **reálné**:

   ![Zahájení přidávání nového skutečného klimatizačního zařízení](media/tutorial-add-device/newreal.png)

3. Zadejte vlastní **ID zařízení** (je třeba zadat malými písmeny) nebo použijte navrhovanou hodnotu. Můžete také zadat **název zařízení** pro nové zařízení a zvolit **vytvořit**.

   ![Přejmenování zařízení](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Konfigurace skutečného zařízení

Skutečné zařízení se vytvoří ze šablony **Connected Air Conditioner**. Na stránce **Settings** (Nastavení) můžete nakonfigurovat zařízení a nastavit hodnoty vlastností pro záznam informací o zařízení.

1. Na stránce **Settings** si všimněte, že stav nastavení **Set temperature** (Nastavit teplotu) je **no update** (Bez aktualizace). Tento stav zůstává beze změny až do té doby, než se skutečné zařízení připojí k aplikaci a potvrdí, že na nastavení reaguje.

    ![Nastavení zobrazující synchronizaci](media/tutorial-add-device/settingssyncing.png)

2. Na stránce **vlastnosti** nového reálného zařízení jsou upravitelné vlastnosti umístění služby a datum poslední služby. Pole se sériovým číslem a verzí firmwaru budou prázdné, dokud se zařízení nepřipojí k aplikaci. Tyto hodnoty jen pro čtení jsou odesílány ze zařízení a nelze je upravovat.

    ![Vlastnosti skutečného zařízení](media/tutorial-add-device/setproperties1.png)

3. Pro své skutečné zařízení můžete zobrazit stránky **Measurements** (Měření), **Rules** (Pravidla) a **Dashboard** (Řídicí panel).

## <a name="prepare-the-client-code"></a>Příprava klientského kódu

Vzorový kód v tomto článku je napsán v [Node. js](https://nodejs.org/) a zobrazuje dostatek kódu pro zařízení:

* Připojit se k aplikaci Azure IoT Central
* Odeslat teplotní telemetrii jako připojené klimatizační zařízení
* Odeslat vlastnosti zařízení do aplikace Azure IoT Central
* Odpovědět operátorovi, který používá nastavení **Set Temperature**
* Zpracovat příkaz Echo z aplikace Azure IoT Central

Články uvedené v části [Další kroky](#next-steps) obsahují více kompletních ukázek a zobrazují další programovací jazyky. Další informace o tom, jak se zařízení připojují k aplikaci Azure IoT Central, najdete v článku věnovaném [možnostem připojení zařízení](concepts-connectivity.md).

Následující postup popisuje, jak připravit ukázku [Node.js](https://nodejs.org/):

### <a name="get-the-device-connection-information"></a>Získat informace o připojení zařízení

1. Připojovací řetězec pro instanci zařízení ve vaší aplikaci se vygeneruje z informací o zařízení, které poskytuje IoT Central.

   Na obrazovce zařízení skutečné připojené klimatizace zvolte **Connect** (Připojit).

   ![Stránka zařízení s odkazem pro zobrazení informací o připojení](media/tutorial-add-device/connectionlink.png)

1. Na stránce **připojení zařízení** si poznamenejte **ID oboru**, **ID zařízení**a hodnoty **primárního klíče** . Tyto hodnoty použijete později v tomto kurzu.

   ![Podrobnosti připojení](media/tutorial-add-device/device-connect.png)

### <a name="prepare-the-nodejs-project"></a>Příprava projektu Node. js

1. Ve vývojovém počítači vytvořte složku s názvem `connectedairconditioner`.

1. V prostředí příkazového řádku přejděte na složku `connectedairconditioner`, kterou jste vytvořili.

1. Pokud chcete inicializovat projekt Node.js, spusťte následující příkaz a přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
      ```

1. Potřebné balíčky nainstalujte spuštěním následujícího příkazu:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. V textovém editoru vytvořte soubor s názvem **ConnectedAirConditioner.js** ve složce `connectedairconditioner`.

1. Na začátek souboru **ConnectedAirConditioner.js** přidejte následující příkazy `require`:

    ```javascript
    'use strict';

    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Do souboru přidejte následující deklarace proměnných. Nahraďte zástupné symboly `{your Scope ID}`, `{your Device ID}` a `{your Primary Key}` informacemi o připojení zařízení, které jste si dříve poznamenali:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    ```

1. Uložte změny, které jste až dosud provedli, ale ponechejte soubor otevřený.

## <a name="review-client-code"></a>Kontrola klientského kódu

V předchozí části jste vytvořili kostru projektu Node. js pro aplikaci zařízení, která se připojuje k vaší aplikaci Azure IoT Central. Dalším krokem je přidání kódu do:

* Připojit se k aplikaci Azure IoT Central
* Odeslat telemetrii do aplikace Azure IoT Central
* Odeslat vlastnosti zařízení do aplikace Azure IoT Central
* Přijmout nastavení z aplikace Azure IoT Central
* Zpracovat příkaz Echo z aplikace Azure IoT Central

1. Pokud chcete odeslat telemetrii do aplikace Azure IoT Central, přidejte následující kód do souboru **ConnectedAirConditioner.js**:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
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
    * Pošle potvrzení zpátky do vaší aplikace Azure IoT Central.

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

        // Create handler for countdown command
        hubClient.onDeviceMethod('echo', onCommandEcho);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };
    ```

1. Zaregistrujte a připojte zařízení k aplikaci IoT Central:

    ```javascript
    // Start the device (connect it to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.assignedHub);
        console.log('deviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

1. Uložte změny, které jste udělali.

## <a name="run-the-client-code"></a>Spustit klientský kód

Nyní můžete spustit kód klienta a zjistit, jak komunikuje s aplikací IoT Central:

1. Ukázku spustíte tak, že v prostředí příkazového řádku spustíte následující příkaz:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Zkontrolujte, že se nacházíte ve složce `connectedairconditioner`, když spouštíte tento příkaz.

1. Aplikace zobrazí výstup do konzoly:

   ![Výstup klientské aplikace](media/tutorial-add-device/output.png)

1. Přibližně po 30 sekundách uvidíte telemetrii na stránce zařízení **Measurements**:

   ![Skutečná telemetrie](media/tutorial-add-device/realtelemetry.png)

1. Na stránce **Settings** uvidíte, že nastavení je nyní synchronizováno. Při prvním připojení zařízení obdrželo hodnotu nastavení a potvrdilo změnu:

   ![Nastavení synchronizováno](media/tutorial-add-device/settingsynced.png)

1. Na stránce **Settings** nastavte teplotu zařízení na **95** a zvolte **Update device** (Aktualizovat zařízení). Ukázková aplikace obdrží tuto změnu a zpracuje ji:

   ![Příjem a zpracování nastavení](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Existují dvě zprávy týkající se aktualizace nastavení. Jedna zpráva se používá při odeslání stavu `pending` a druhá při odeslání stavu `completed`.

1. Na stránce **Measurements** si můžete všimnout, že zařízení posílá vyšší teplotní hodnoty:

    ![Teplotní telemetrie je nyní vyšší.](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="nextstepaction"]
> * Přidat nové skutečné zařízení
> * Nakonfigurovat nové zařízení
> * Získat připojovací řetězec pro skutečné zařízení z aplikace
> * Porozumět, jak se klientský kód mapuje k aplikaci
> * Konfigurace klientského kódu pro skutečné zařízení

Teď, když jste připojili reálné zařízení k aplikaci Azure IoT Central, tady je několik navrhovaných dalších kroků.

Jako operátor se naučíte tyto postupy:

* [Správa zařízení](howto-manage-devices.md)
* [Použití sad zařízení](howto-use-device-sets.md)
* [Vytvoření vlastní analýzy](howto-use-device-sets.md)

Jako vývojář zařízení se naučíte tyto postupy:

* [Příprava a připojení zařízení DevKit (C)](howto-connect-devkit.md)
* [Příprava a připojení maliny PI (Python)](howto-connect-raspberry-pi-python.md)
* [Příprava a připojení maliny PI (C#)](howto-connect-raspberry-pi-csharp.md)
* [Příprava a připojení zařízení s Windows 10 IoT Core (C#)](howto-connect-windowsiotcore.md)
* [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs.md)
