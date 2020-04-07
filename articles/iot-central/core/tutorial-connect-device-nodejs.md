---
title: Kurz – připojení obecné klientské aplikace Node.js k Azure IoT Central | Dokumenty společnosti Microsoft
description: Tento kurz ukazuje, jak jako vývojář zařízení připojit zařízení s klientskou aplikací Node.js k vaší aplikaci Azure IoT Central. Šablonu zařízení vytvoříte importem modelu schopností zařízení a přidáte zobrazení, která vám umožní pracovat s připojeným zařízením.
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b7aebb5b9c1aa6566cedda869f97f2d1aa20df83
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673954"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Kurz: Vytvoření a připojení klientské aplikace Node.js k aplikaci Azure IoT Central (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

Tento kurz ukazuje, jak jako vývojář zařízení připojit klientskou aplikaci Node.js k vaší aplikaci Azure IoT Central. Aplikace Node.js simuluje chování zařízení senzoru prostředí. Ukázkový _model schopností zařízení_ se používá k vytvoření šablony _zařízení_ v ioT central. Do šablony zařízení přidáte zobrazení, která operátorovi umožní pracovat se zařízením.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importujte model schopností zařízení a vytvořte šablonu zařízení.
> * Přidejte do šablony zařízení výchozí a vlastní zobrazení.
> * Publikujte šablonu zařízení a přidejte skutečné zařízení do aplikace IoT Central.
> * Vytvořte a spusťte kód zařízení Node.js a podívejte se, jak se připojuje k vaší aplikaci IoT Central.
> * Zobrazení simulované telemetrie odeslané ze zařízení.
> * Ke správě vlastností zařízení použijte zobrazení.
> * Volání synchronní a asynchronní příkazy pro řízení zařízení.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

* Aplikace Azure IoT Central vytvořená pomocí vlastní šablony **aplikace.** Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
* Vývojový počítač s [nainstalovanou aplikací Node.js](https://nodejs.org/) verze 10.0.0 nebo novější. Můžete spustit `node --version` v příkazovém řádku a zkontrolovat svou verzi. Pokyny v tomto kurzu předpokládají, že používáte příkaz **uzlu** na příkazovém řádku systému Windows. Node.js však můžete použít v mnoha jiných operačních systémech.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Následující kroky ukazují, jak vytvořit klientskou aplikaci Node.js, která se připojuje ke skutečnému zařízení, které jste do aplikace přidali. Tato aplikace Node.js simuluje chování skutečného zařízení.

1. V prostředí příkazového řádku přejděte do složky, kterou jste vytvořili `environmental-sensor` dříve.

1. Chcete-li inicializovat projekt Node.js a nainstalovat požadované závislosti, spusťte následující `npm init`příkazy – při spuštění přijměte všechny výchozí možnosti :

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Vytvořte soubor s názvem **environmentalSensor.js** ve `environmental-sensor` složce.

1. Na začátek souboru `require` **environmentalSensor.js** přidejte následující příkazy:

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

    Aktualizujte zástupné symboly `{your Scope ID}` `{your Device ID}`a `{your Primary Key}` hodnoty, které jste si dříve poznamenali. V této ukázce `targetTemperature` inicializovat na nulu, můžete použít aktuální čtení ze zařízení nebo hodnotu z dvojčete zařízení.

1. Pokud chcete do aplikace Azure IoT Central odeslat simulovanou telemetrii, přidejte do souboru následující funkci:

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

    Názvy položek telemetrie`temp` `humid`( a ) se musí shodovat s názvy použitými v šabloně zařízení.

1. Pokud chcete do aplikace Azure IoT Central odeslat vlastnosti dvojčete zařízení, přidejte do souboru následující funkci:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central používá dvojčata zařízení k synchronizaci hodnot vlastností mezi zařízením a aplikací IoT Central. Hodnoty vlastností zařízení používají vlastnosti dvojčete zařízení. Zapisovatelné vlastnosti používají hlášené i požadované vlastnosti dvojčete zařízení.

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

    Když operátor nastaví zapisovatelnou vlastnost v aplikaci IoT Central, aplikace použije k odeslání hodnoty do zařízení požadovanou vlastnost dvojčete zařízení. Zařízení pak odpoví pomocí služby dvojčete zařízení. Když IoT Central obdrží ohlášenou hodnotu vlastnosti, aktualizuje zobrazení vlastností se stavem **synchronizované**.

    Názvy vlastností`name` ( `brightness`a ) se musí shodovat s názvy použitými v šabloně zařízení.

1. Přidejte následující kód pro zpracování příkazů odeslaných z aplikace IoT Central:

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

    Názvy`blink`příkazů ( , `turnon` `turnoff`, `rundiagnostics`, a ) se musí shodovat s názvy použitými v šabloně zařízení.

    V současné době IoT Central nepoužívá schéma odezvy definované v modelu schopností zařízení. Pro synchronní příkaz může být datová část odpovědi libovolná platná json. Pro asynchronní příkaz zařízení by měla okamžitě vrátit odpověď 202, následovaný ohlášenou aktualizací vlastností po dokončení práce. Formát aktualizace ohlášené vlastnosti je:

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

Chcete-li spustit klientskou aplikaci zařízení, spusťte v prostředí příkazového řádku následující příkaz:

```cmd/sh
node environmentalSensor.js
```

Můžete vidět zařízení se připojí k aplikaci Azure IoT Central a začne odesílat telemetrii:

![Spuštění klientské aplikace](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Můžete vidět, jak zařízení reaguje na příkazy a aktualizace vlastností:

![Sledování klientské aplikace](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>Další kroky

Další informace o modelech funkcí zařízení a o tom, jak vytvořit vlastní šablony zařízení, najdete v návodu:

> [!div class="nextstepaction"]
> [Definování nového typu zařízení IoT](./howto-set-up-template.md)
