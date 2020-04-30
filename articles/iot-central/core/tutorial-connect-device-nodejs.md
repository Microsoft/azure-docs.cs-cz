---
title: Kurz – připojení Obecné klientské aplikace Node. js do Azure IoT Central | Microsoft Docs
description: V tomto kurzu se dozvíte, jak jako vývojář zařízení připojit zařízení s klientskou aplikací Node. js k vaší aplikaci Azure IoT Central. Šablonu zařízení vytvoříte tak, že naimportujete model schopností zařízení a přidáte zobrazení, která vám umožní pracovat s připojeným zařízením.
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt
ms.openlocfilehash: a8c5d9479585c0a519d0ad05a4d73f3f15b21287
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81758196"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Kurz: vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central (Node. js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

V tomto kurzu se dozvíte, jak jako vývojář zařízení připojit klientskou aplikaci Node. js k vaší aplikaci Azure IoT Central. Aplikace Node. js simuluje chování zařízení snímače životního prostředí. Pomocí ukázkového _modelu schopností zařízení_ vytvoříte _šablonu zařízení_ v IoT Central. Přidáním zobrazení do šablony zařízení povolíte operátorovi komunikovat se zařízením.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importujte model schopností zařízení a vytvořte šablonu zařízení.
> * Přidejte do šablony zařízení výchozí a vlastní zobrazení.
> * Publikujte šablonu zařízení a přidejte do aplikace IoT Central reálné zařízení.
> * Vytvořte a spusťte kód zařízení Node. js a podívejte se, jak se připojí k vaší IoT Central aplikaci.
> * Zobrazit simulovanou telemetrii odeslanou ze zařízení.
> * Pomocí zobrazení můžete spravovat vlastnosti zařízení.
> * Pro řízení zařízení zavolejte synchronní a asynchronní příkazy.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

* Aplikace Azure IoT Central vytvořená pomocí šablony **vlastní aplikace** . Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
* Vývojový počítač s nainstalovanou aplikací [Node. js](https://nodejs.org/) verze 10.0.0 nebo novější. Můžete spustit `node --version` na příkazovém řádku a ověřit svou verzi. Pokyny v tomto kurzu předpokládají, že jste spustili příkaz **Node** na příkazovém řádku Windows. V mnoha dalších operačních systémech ale můžete použít Node. js.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Následující kroky ukazují, jak vytvořit klientskou aplikaci Node. js, která se připojuje k reálnému zařízení, které jste přidali do aplikace. Tato aplikace Node. js simuluje chování reálného zařízení.

1. V prostředí příkazového řádku přejděte do `environmental-sensor` složky, kterou jste předtím vytvořili.

1. Chcete-li inicializovat projekt Node. js a nainstalovat požadované závislosti, spusťte následující příkazy – přijměte všechny výchozí možnosti při spuštění `npm init`:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Ve `environmental-sensor` složce vytvořte soubor s názvem **environmentalSensor. js** .

1. Na začátek souboru `require` **environmentalSensor. js** přidejte následující příkazy:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Do souboru přidejte následující deklarace proměnných:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    Aktualizujte `{your Scope ID}`zástupné `{your Device ID}`symboly, `{your Primary Key}` a s hodnotami, které jste si poznamenali dříve. V této ukázce se inicializujete `targetTemperature` na nulu, můžete použít aktuální čtení ze zařízení nebo hodnotu z vlákna zařízení.

1. K odeslání simulované telemetrie do aplikace Azure IoT Central přidejte do souboru následující funkci:

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Názvy položek telemetrie (`temp` a `humid`) se musí shodovat s názvy použitými v šabloně zařízení.

1. Pokud chcete do vaší aplikace Azure IoT Central odeslat vlastnosti, které jsou v zařízení, přidejte do souboru následující funkci:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central používá vlákna zařízení k synchronizaci hodnot vlastností mezi zařízením a aplikací IoT Central. Hodnoty vlastností zařízení používají nedokončené hlášené vlastnosti zařízení. Zapisovatelné vlastnosti používají v zařízení nahlášené vlákna a požadované vlastnosti.

1. Chcete-li definovat a zpracovat zapisovatelné vlastnosti, na které zařízení reaguje, přidejte následující kód:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            writeableProperties[setting](desiredChange[setting].value, (newValue, status) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Když operátor nastaví vlastnost s možností zápisu v aplikaci IoT Central, aplikace použije pro odeslání hodnoty do zařízení hodnotu, která je typu vlákna. Zařízení pak odpoví pomocí vlastnosti nedokončené hlášené v zařízení. Když IoT Central obdrží hodnotu hlášené vlastnosti, aktualizuje zobrazení vlastností se stavem **Synchronizovaný**.

    Názvy vlastností (`name` a `brightness`) se musí shodovat s názvy použitými v šabloně zařízení.

1. Přidejte následující kód, který bude zpracovávat příkazy odesílané z aplikace IoT Central:

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    `blink`Názvy příkazů ( `turnon`,, `turnoff`a `rundiagnostics`) se musí shodovat s názvy použitými v šabloně zařízení.

    V současné době IoT Central nepoužívá schéma odpovědi definované v modelu schopností zařízení. V případě synchronního příkazu může být datová část odpovědi libovolný platný kód JSON. V případě asynchronního příkazu by zařízení mělo vrátit odpověď 202 hned po dokončení práce, po které následuje aktualizace hlášené vlastnosti. Formát hlášené aktualizace vlastnosti:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Operátor může zobrazit datovou část odpovědi v historii příkazů.

1. Přidejte následující kód, abyste dokončili připojení k aplikaci Azure IoT Central a připojili funkce v klientském kódu:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Spuštění aplikace Node.js

Pokud chcete spustit klientskou aplikaci pro zařízení, spusťte v prostředí příkazového řádku následující příkaz:

```cmd/sh
node environmentalSensor.js
```

Můžete vidět, že se zařízení připojí k aplikaci Azure IoT Central a začne odesílat telemetrii:

![Spuštění klientské aplikace](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Můžete zjistit, jak zařízení reaguje na příkazy a aktualizace vlastností:

![Sledování klientské aplikace](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>Další kroky

Jako vývojář zařízení teď, když jste se naučili základy vytvoření zařízení pomocí Node. js, jsme si udělali několik navrhovaných dalších kroků:

- Naučte se, jak připojit reálné zařízení k IoT Central v tématu [připojení zařízení IoT DevKit k vaší aplikaci Azure IoT Central](./howto-connect-devkit.md) .
- Další informace o registraci zařízení pomocí IoT Central a o tom, jak IoT Central zabezpečují připojení zařízení, najdete v tématu věnovaném [připojení k Azure IoT Central](./concepts-get-connected.md) .

Pokud budete chtít pokračovat v IoT Central výukových kurzů a další informace o vytváření řešení IoT Central najdete tady:

> [!div class="nextstepaction"]
> [Vytvoření šablony zařízení brány](./tutorial-define-gateway-device-type.md)
