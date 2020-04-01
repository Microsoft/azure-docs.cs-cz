---
title: Kurz – připojení obecné klientské aplikace Node.js k Azure IoT Central | Dokumenty společnosti Microsoft
description: Tento kurz ukazuje, jak jako vývojář zařízení připojit zařízení s klientskou aplikací Node.js k vaší aplikaci Azure IoT Central. Šablonu zařízení vytvoříte importem modelu schopností zařízení a přidáte zobrazení, která vám umožní pracovat s připojeným zařízením.
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77624540"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Kurz: Vytvoření a připojení klientské aplikace Node.js k aplikaci Azure IoT Central (Node.js)

Tento kurz ukazuje, jak jako vývojář zařízení připojit klientskou aplikaci Node.js k vaší aplikaci Azure IoT Central. Aplikace Node.js simuluje chování skutečného zařízení. Vzorové _zařízení funkce modelu_ pro zařízení senzorprostředí k vytvoření šablony _zařízení_ v IoT Central. Do šablony zařízení přidáte zobrazení, která vám umožní vizualizovat telemetrii zařízení, spravovat vlastnosti zařízení a používat příkazy k ovládání zařízení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importujte model schopností zařízení a vytvořte šablonu zařízení.
> * Přidejte do šablony zařízení výchozí a vlastní zobrazení.
> * Publikujte šablonu zařízení a přidejte skutečné zařízení do aplikace IoT Central.
> * Vytvořte a spusťte kód zařízení Node.js a podívejte se, jak se připojuje k vaší aplikaci IoT Central.
> * Zobrazení simulované telemetrie, kterou zařízení odesílá.
> * Ke správě vlastností zařízení použijte zobrazení.
> * Volání příkazů pro ovládání zařízení.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

* Aplikace Azure IoT Central vytvořená pomocí **Vlastní aplikace **šablony. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
* Vývojový počítač s [nainstalovanou aplikací Node.js](https://nodejs.org/) verze 10.0.0 nebo novější. Můžete spustit `node --version` v příkazovém řádku a zkontrolovat svou verzi. Node.js je k dispozici pro širokou škálu operačních systémů. Pokyny v tomto kurzu předpokládají, že používáte příkaz **uzlu** na příkazovém řádku systému Windows. Soubor Node.js můžete použít v různých operačních systémech.

## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

Vytvořte složku `environmental-sensor` volanou v místním počítači.

Stáhněte si [model schopnosti životního prostředí JSON](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) soubor a uložte jej do `environmental-sensor` složky.

Pomocí textového `{YOUR_COMPANY_NAME_HERE}` editoru nahraďte dvě instance názvu `EnvironmentalSensorInline.capabilitymodel.json` společnosti v souboru, který jste stáhli.

V aplikaci Azure IoT Central vytvořte šablonu zařízení `EnvironmentalSensorInline.capabilitymodel.json` nazvanou Senzor *prostředí* importem souboru modelu schopností zařízení:

![Šablona zařízení s importovaným modelem schopností zařízení](./media/tutorial-connect-device/device-template.png)

Model schopností zařízení obsahuje dvě rozhraní: standardní informační rozhraní **zařízení** a vlastní rozhraní **senzoru prostředí.** Rozhraní **senzoru prostředí** definuje následující možnosti:

| Typ | Zobrazovaný název | Popis |
| ---- | ------------ | ----------- |
| Vlastnost | Stav zařízení     | Stav zařízení. K dispozici jsou dva stavy online/offline. |
| Vlastnost | Jméno zákazníka    | Jméno zákazníka, který zařízení aktuálně provozuje. |
| Vlastnost | Úroveň jasu | Úroveň jasu světla na zařízení. Může být specifikován jako 1 (vysoká), 2 (střední), 3 (nízká). |
| Telemetrie | Teplota | Aktuální teplota na zařízení. |
| Telemetrie | Vlhkost    | Aktuální vlhkost na zařízení. |
| Příkaz | Blikat          | Začněte blikat LED diodu pro daný časový interval. |
| Příkaz | odbočka         | Zapněte led diodu na zařízení. |
| Příkaz | Odbočku        | Vypněte led světlo na zařízení. |
| Příkaz | rundiagnostics | Tento příkaz spustí spuštění diagnostiky. |

Pokud chcete přizpůsobit, jak se vlastnost **Stav zařízení** zobrazí ve vaší aplikaci IoT Central, vyberte **Přizpůsobit** v šabloně zařízení. Rozbalte položku **Stav zařízení,** zadejte _online_ jako **true název** a _offline_ jako **false name**. Pak uložte změny:

![Přizpůsobení šablony zařízení](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Vytváření zobrazení

Zobrazení umožňují interakci se zařízeními připojenými k aplikaci IoT Central. Můžete mít například zobrazení, která zobrazují telemetrii, zobrazení, která zobrazují vlastnosti, a zobrazení, která umožňují upravovat zapisovatelné a cloudové vlastnosti. Zobrazení jsou součástí šablony zařízení.

Chcete-li do šablony **zařízení environmentálního senzoru** přidat některá výchozí zobrazení, přejděte do šablony zařízení, vyberte **Zobrazení**a vyberte dlaždici Generovat **výchozí zobrazení.** Zkontrolujte, zda jsou **zapnuty** **položky** **Přehled** a Informace , a pak vyberte **generovat výchozí zobrazení řídicího panelu**. Nyní máte v šabloně definována dvě výchozí zobrazení.

Rozhraní **environmentálního senzoru** obsahuje dvě zapisovatelné vlastnosti - **jméno zákazníka** a **úroveň jasu**. Chcete-li vytvořit zobrazení, můžete použít k úpravám těchto vlastností:

1. Vyberte **Zobrazení** a pak vyberte **dlaždici Editing device a cloud data.**

1. Jako název formuláře zadejte _Vlastnosti._

1. Vyberte vlastnosti **Úroveň jasu** a **Jméno zákazníka.** Pak vyberte **Přidat oddíl**.

1. Uložte provedené změny.

![Přidání zobrazení pro povolení úprav vlastností](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>Publikování šablony

Před přidáním zařízení do aplikace IoT Central, která používá šablonu **zařízení environmentálního senzoru,** je třeba šablonu publikovat.

V šabloně zařízení vyberte **Publikovat**. V panelu, který zobrazuje změny, které mají být publikovány, vyberte **Publikovat**.

Chcete-li zkontrolovat, zda je šablona připravená k použití, přejděte na stránku **Zařízení** v aplikaci IoT Central. V části **Zařízení** je uveden seznam publikovaných zařízení v aplikaci:

![Publikované šablony na stránce zařízení](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidejte skutečné zařízení do šablony zařízení, kterou jste vytvořili v předchozí části:

1. Na stránce **Zařízení** vyberte šablonu **zařízení environmentálního senzoru.**

1. Vyberte **+ Nový**.

1. Ujistěte se, že **simulované** je **vypnuto**. Pak vyberte **Vytvořit**.

Klikněte na název zařízení a pak vyberte **Připojit**. Poznamenejte si informace o připojení zařízení na stránce **Připojení k zařízení** – obor **ID**, **ID zařízení**a primární **klíč**. Tyto hodnoty potřebujete při vytváření kódu zařízení:

![Informace o připojení zařízení](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Následující kroky ukazují, jak vytvořit klientskou aplikaci Node.js, která implementuje skutečné zařízení, které jste přidali do aplikace. Tato aplikace Node.js simuluje chování skutečného zařízení.

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

1. Pokud chcete odeslat telemetrii do aplikace Azure IoT Central, přidejte do souboru následující funkci:

    ```javascript
    // Send device measurements.
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

1. Pokud chcete do aplikace Azure IoT Central odeslat vlastnosti zařízení, přidejte do souboru následující funkci:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Chcete-li definovat a zpracovat zapisovatelné vlastnosti, na které zařízení reaguje, přidejte následující kód:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
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
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. Přidejte následující kód pro zpracování příkazů odeslaných z aplikace IoT Central:

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
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

    // Handle LED turn off command
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

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
        }
      });
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
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
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

![Spuštění klientské aplikace](media/tutorial-connect-device/run-application.png)

Jako operátor v aplikaci Azure IoT Central můžete:

* Zobrazení telemetrie odeslané zařízením na stránce **Přehled:**

    ![Zobrazení telemetrických dat](media/tutorial-connect-device/view-telemetry.png)

* Aktualizace hodnot zapisovatelných vlastností na stránce **Vlastnosti:**

    ![Aktualizovat vlastnosti](media/tutorial-connect-device/update-properties.png)

    ![Aktualizovat zařízení vlastností](media/tutorial-connect-device/update-properties-device.png)

* Volání příkazů ze stránky **Příkazy:**

    ![Příkaz Mrknutí hovoru](media/tutorial-connect-device/call-command.png)

    ![Volání příkazového zařízení pro blikání](media/tutorial-connect-device/call-command-device.png)

* Zobrazení vlastností zařízení na stránce **Informace:**

    ![Zobrazení vlastností](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Další kroky

Další informace o modelech funkcí zařízení a o tom, jak vytvořit vlastní šablony zařízení, najdete v návodu:

> [!div class="nextstepaction"]
> [Definování nového typu zařízení IoT](./howto-set-up-template.md)
