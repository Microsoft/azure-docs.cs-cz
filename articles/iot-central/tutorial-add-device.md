---
title: Přidání reálného zařízení do aplikace Azure IoT Central | Microsoft Docs
description: Jako operátor přidejte reálné zařízení do aplikace Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 08/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2673d0d2c1cb174316e99a79a10a67347e2bc031
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001350"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Kurz: Přidání reálného zařízení do aplikace Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

V tomto kurzu se dozvíte, jak do IoT Central aplikace Microsoft Azure přidat a nakonfigurovat *reálné zařízení* . V tomto kurzu navedete kód reálného zařízení pomocí Node. js a spustíte kód na stolním počítači. K dokončení tohoto kurzu nepotřebujete samostatné zařízení IoT, jako je například zařízení malin PI nebo MXChip IoT DevKit.

Tento kurz se skládá ze dvou částí:

* Nejdřív se naučíte, jak v aplikaci Azure IoT Central přidat a nakonfigurovat reálné zařízení. Na konci této části načtěte připojovací řetězec, který se použije ve druhé části.
* Jako vývojář zařízení si pak přečtěte informace o kódu na skutečném zařízení. Do vzorového kódu přidáte připojovací řetězec z první části.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat nové reálné zařízení
> * Konfigurace reálného zařízení
> * Získání připojovacího řetězce pro reálné zařízení z aplikace
> * Pochopení způsobu mapování kódu klienta na aplikaci
> * Konfigurace klientského kódu pro reálné zařízení

## <a name="prerequisites"></a>Požadavky

Předtím, než začnete, je potřeba, aby tvůrce vytvořil aplikaci Azure IoT Central v prvním kurzu sestavování: [Definujte nový typ zařízení](tutorial-define-device-type.md) (povinné).

Nainstalujte [Node. js](https://nodejs.org/) verze 8.0.0 nebo novější na svém vývojovém počítači. Můžete spustit `node --version` na příkazovém řádku a ověřit svou verzi. Node. js je k dispozici pro širokou škálu operačních systémů.

## <a name="add-a-real-device"></a>Přidání reálného zařízení

K přidání reálného zařízení do aplikace použijete **připojenou** šablonu zařízení klimatizační aplikace, kterou jste vytvořili v kurzu [definování nového typu zařízení](tutorial-define-device-type.md) .

1. Pokud chcete přidat nové zařízení jako operátor, vyberte v levé navigační nabídce možnost **zařízení** :

   ![Stránka Průzkumník zařízení ukazující připojeného klimatizace](media/tutorial-add-device/explorer.png)

   **Device Explorer** zobrazuje **připojenou** šablonu zařízení klimatizace pro klimatizace a simulované zařízení. Když vytvoříte šablonu zařízení, IoT Central automaticky vytvoří simulované zařízení.

2. Všimněte si, že **připojená** šablona zařízení klimatizace klimatizace je ta, kterou jste vybrali v **Device Explorer**. Pokud chcete začít s připojením reálného zařízení klimatizace, které tuto šablonu používá, vyberte **+** a potom **reálné**:

   ![Začněte přidávat nové a reálné zařízení s přípravou připojeného klimatizace.](media/tutorial-add-device/newreal.png)

3. Zadejte vlastní **ID zařízení** (je třeba zadat malými písmeny) nebo použijte navrhovanou hodnotu. Můžete také zadat **název zařízení** pro nové zařízení a zvolit **vytvořit**.

   ![Přejmenovat zařízení](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Konfigurace reálného zařízení

Reálné zařízení se vytvoří z **připojené** šablony zařízení klimatizačního modulu. Pomocí **Nastavení** můžete nakonfigurovat zařízení a nastavit hodnoty vlastností tak, aby se zaznamenaly informace o zařízení.

1. Na stránce **Nastavení** si všimněte, že stav nastavení **teploty** není **žádná aktualizace**. Zůstane v tomto stavu, dokud se reálné zařízení nepřipojí k aplikaci a potvrdí, že se na nastavení jednalo.

    ![Nastavení – zobrazit synchronizaci](media/tutorial-add-device/settingssyncing.png)

2. Na stránce **vlastnosti** nového reálného zařízení jsou upravitelné vlastnosti umístění služby a datum poslední služby. Pole sériového čísla a verze firmwaru jsou prázdná, dokud se zařízení nepřipojí k aplikaci. Tyto hodnoty jen pro čtení jsou odesílány ze zařízení a nelze je upravovat.

    ![Vlastnosti zařízení reálného zařízení](media/tutorial-add-device/setproperties1.png)

3. Můžete zobrazit **měření**, **pravidla**a stránky **řídicího panelu** pro skutečné zařízení.

## <a name="prepare-the-client-code"></a>Příprava klientského kódu

Vzorový kód v tomto článku je napsán v [Node. js](https://nodejs.org/) a zobrazuje dostatek kódu pro zařízení:

* Připojte se k aplikaci Azure IoT Central.
* Poslat telemetrie na teplotu jako připojená zařízení klimatizačního zařízení.
* Odeslání vlastností zařízení do aplikace Azure IoT Central.
* Odpovězte na operátor, který používá nastavení **teploty** .
* Zpracování příkazu echo z aplikace Azure IoT Central.

Články uvedené v části [Další kroky](#next-steps) obsahují více kompletních ukázek a zobrazují další programovací jazyky. Další informace o tom, jak se zařízení připojují k Azure IoT Central, najdete v článku věnovaném [připojení zařízení](concepts-connectivity.md) .

Následující kroky ukazují, jak připravit ukázku [Node. js](https://nodejs.org/) :

### <a name="get-the-device-connection-information"></a>Získat informace o připojení zařízení

1. Připojovací řetězec pro instanci zařízení ve vaší aplikaci je vygenerovaný z informací o zařízení, které poskytuje IoT Central.

   Na obrazovce zařízení pro reálný klimatizace v reálném čase klikněte na **připojit**.

   ![Stránka zařízení, na které se zobrazuje odkaz zobrazit informace o připojení](media/tutorial-add-device/connectionlink.png)

1. Na stránce **připojení zařízení** si poznamenejte **ID oboru**, **ID zařízení**a hodnoty **primárního klíče** . Tyto hodnoty použijete později v tomto kurzu.

   ![Podrobnosti připojení](media/tutorial-add-device/device-connect.png)

### <a name="prepare-the-nodejs-project"></a>Příprava projektu Node. js

1. Ve vývojovém počítači vytvořte složku s názvem `connectedairconditioner`.

1. V prostředí příkazového řádku přejděte do složky @no__t 0, kterou jste vytvořili.

1. Chcete-li inicializovat projekt Node. js, spusťte následující příkaz, který přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
      ```

1. Potřebné balíčky nainstalujete spuštěním následujícího příkazu:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Pomocí textového editoru vytvořte ve složce `connectedairconditioner` soubor s názvem **ConnectedAirConditioner. js** .

1. Na začátek souboru **ConnectedAirConditioner. js** přidejte následující příkazy `require`:

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

1. Uložte změny, které jste prozatím udělali, ale soubor nechte otevřený.

## <a name="review-client-code"></a>Kontrola klientského kódu

V předchozí části jste vytvořili kostru projektu Node. js pro aplikaci zařízení, která se připojuje k vaší aplikaci Azure IoT Central. Dalším krokem je přidání kódu do:

* Připojte se k aplikaci Azure IoT Central.
* Pošlete telemetrii do aplikace Azure IoT Central.
* Odeslání vlastností zařízení do aplikace Azure IoT Central.
* Příjem nastavení z vaší aplikace IoT Central Azure
* Zpracování příkazu echo z aplikace Azure IoT Central.

1. K odeslání telemetrie o teplotě do aplikace Azure IoT Central přidejte do souboru **ConnectedAirConditioner. js** následující kód:

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

    Název pole ve formátu JSON, který odesíláte, se musí shodovat s názvem pole, které jste zadali pro telemetrii teploty v šabloně zařízení. V tomto příkladu je název pole **teploty**.

1. K odeslání vlastností zařízení, jako jsou **firmwareVersion** a **sériové**, přidejte následující definici:

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

1. Pokud chcete definovat nastavení, které zařízení podporuje, například **setTemperature**, přidejte následující definici:

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

1. Pokud chcete zpracovat nastavení odesílaná z Azure IoT Central, přidejte následující funkci, která vyhledá a provede příslušný kód zařízení:

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

    * Sleduje službu Azure IoT Central posílá požadovanou vlastnost.
    * Vyhledá příslušnou funkci, která se zavolá, aby se změna nastavení natáhla.
    * Pošle potvrzení zpátky do vaší aplikace Azure IoT Central.

1. Pokud chcete reagovat na příkaz, jako je například **echo** z aplikace Azure IoT Central, přidejte následující definici:

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
    }
    ```

1. Přidejte následující kód pro dokončení připojení k Azure IoT Central a zapojte funkce v klientském kódu:

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

1. Ukázku spustíte tak, že v prostředí příkazového řádku zadáte následující příkaz:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Když spustíte tento příkaz, ujistěte se, že jste ve složce `connectedairconditioner`.

1. Aplikace vytiskne výstup do konzoly:

   ![Výstup klientské aplikace](media/tutorial-add-device/output.png)

1. Po přibližně 30 sekundách se telemetrie zobrazí na stránce **měření** zařízení:

   ![Skutečná telemetrie](media/tutorial-add-device/realtelemetry.png)

1. Na stránce **Nastavení** vidíte, že nastavení je nyní synchronizováno. Když se zařízení poprvé připojí, obdrží hodnotu nastavení a potvrdí změnu:

   ![Nastavení synchronizováno](media/tutorial-add-device/settingsynced.png)

1. Na stránce **Nastavení** nastavte teplotu zařízení na **95** a vyberte **aktualizovat zařízení**. Vaše ukázková aplikace přijme a zpracuje tuto změnu:

   ![Nastavení příjmu a zpracování](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > K dispozici jsou dvě zprávy o nastavení aktualizace. Jedna při odeslání stavu `pending` a jedna při odeslání stavu `completed`.

1. Na stránce **měření** vidíte, že zařízení posílá vyšší teplotní hodnoty:

    ![Telemetrie teplot je teď vyšší.](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak:

> [!div class="nextstepaction"]
> * Přidat nové reálné zařízení
> * Konfigurace nového zařízení
> * Získání připojovacího řetězce pro reálné zařízení z aplikace
> * Pochopení způsobu mapování kódu klienta na aplikaci
> * Konfigurace klientského kódu pro reálné zařízení

Teď, když jste připojili reálné zařízení k aplikaci Azure IoT Central, tady je několik navrhovaných dalších kroků.

Jako operátor můžete zjistit, jak:

* [Správa zařízení](howto-manage-devices.md)
* [Použití sad zařízení](howto-use-device-sets.md)
* [Vytváření vlastních analýz](howto-use-device-sets.md)

Jako vývojář zařízení můžete zjistit, jak:

* [Příprava a připojení zařízení DevKit (C)](howto-connect-devkit.md)
* [Příprava a připojení maliny PI (Python)](howto-connect-raspberry-pi-python.md)
* [Příprava a připojení maliny PI (C#)](howto-connect-raspberry-pi-csharp.md)
* [Příprava a připojení zařízení s Windows 10 IoT Core (C#)](howto-connect-windowsiotcore.md)
* [Připojení obecného klienta Node. js k aplikaci Azure IoT Central](howto-connect-nodejs.md)
