---
title: Kurz – připojení Obecné klientské aplikace Node. js do Azure IoT Central | Microsoft Docs
description: V tomto kurzu se dozvíte, jak jako vývojář zařízení připojit zařízení s klientskou aplikací Node. js k vaší aplikaci Azure IoT Central. Šablonu zařízení vytvoříte tak, že naimportujete model schopností zařízení a přidáte zobrazení, která vám umožní pracovat s připojeným zařízením.
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624540"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Kurz: vytvoření a připojení klientské aplikace Node. js do aplikace Azure IoT Central (Node. js)

V tomto kurzu se dozvíte, jak jako vývojář zařízení připojit klientskou aplikaci Node. js k vaší aplikaci Azure IoT Central. Aplikace Node. js simuluje chování reálného zařízení. Pomocí ukázkového _modelu schopností zařízení_ pro zařízení se snímačem životního prostředí vytvoříte _šablonu zařízení_ v IoT Central. Do šablony zařízení přidáte zobrazení, abyste mohli vizualizovat telemetrii zařízení, spravovat vlastnosti zařízení a používat příkazy k řízení zařízení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importujte model schopností zařízení a vytvořte šablonu zařízení.
> * Přidejte do šablony zařízení výchozí a vlastní zobrazení.
> * Publikujte šablonu zařízení a přidejte do aplikace IoT Central reálné zařízení.
> * Vytvořte a spusťte kód zařízení Node. js a podívejte se, jak se připojí k vaší IoT Central aplikaci.
> * Podívejte se na simulovanou telemetrii, kterou zařízení odesílá.
> * Pomocí zobrazení můžete spravovat vlastnosti zařízení.
> * Zavolejte příkazy pro řízení zařízení.

## <a name="prerequisites"></a>Předpoklady

K dokončení kroků v tomto článku budete potřebovat následující:

* Aplikace Azure IoT Central vytvořená pomocí šablony * * vlastní aplikace * *. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
* Vývojový počítač s nainstalovanou aplikací [Node. js](https://nodejs.org/) verze 10.0.0 nebo novější. Můžete spustit `node --version` na příkazovém řádku a ověřit svou verzi. Node.js je k dispozici pro širokou škálu operačních systémů. Pokyny v tomto kurzu předpokládají, že jste spustili příkaz **Node** na příkazovém řádku Windows. Node. js můžete použít v různých operačních systémech.

## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

Na místním počítači vytvořte složku s názvem `environmental-sensor`.

Stáhněte si soubor JSON [modelu schopností environmentálního senzoru](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) a uložte ho do složky `environmental-sensor`.

Pomocí textového editoru nahraďte dvě instance `{YOUR_COMPANY_NAME_HERE}` názvem vaší společnosti v souboru `EnvironmentalSensorInline.capabilitymodel.json`, který jste stáhli.

V aplikaci Azure IoT Central vytvořte šablonu zařízení s názvem *senzor okolního prostředí* importem souboru modelu schopností `EnvironmentalSensorInline.capabilitymodel.json` zařízení:

![Šablona zařízení s importovaným modelem schopností zařízení](./media/tutorial-connect-device/device-template.png)

Model schopností zařízení zahrnuje dvě rozhraní: standardní **informace o zařízení** a vlastní rozhraní **snímače životního prostředí** . Rozhraní **snímače prostředí** definuje následující možnosti:

| Typ | Zobrazovaný název | Popis |
| ---- | ------------ | ----------- |
| Vlastnost | Stav zařízení     | Stav zařízení. K dispozici jsou dva stavy online/offline. |
| Vlastnost | Název zákazníka    | Jméno zákazníka, který aktuálně provozuje zařízení. |
| Vlastnost | Úroveň jasu | Úroveň jasu světla na zařízení. Může být zadáno jako 1 (vysoká), 2 (střední), 3 (nízká). |
| Telemetrická data | Teplota | Aktuální teplota zařízení. |
| Telemetrická data | Vlhkost    | Aktuální vlhkost na zařízení. |
| Příkaz | blikají          | Začátek blikání indikátoru LED pro daný časový interval. |
| Příkaz | turnon         | Zapněte na zařízení světlo LED. |
| Příkaz | turnoff        | Vypněte světlo LED na zařízení. |
| Příkaz | rundiagnostics | Tento příkaz spustí spuštění diagnostiky. |

Pokud chcete přizpůsobit, jak se ve vaší aplikaci IoT Central zobrazí vlastnost **stav zařízení** , vyberte **přizpůsobit** v šabloně zařízení. Rozbalte položku **stav zařízení** , zadejte _online_ jako **skutečný název** a _offline_ jako **název false**. Pak změny uložte:

![Přizpůsobení šablony zařízení](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Vytvoření zobrazení

Zobrazení vám umožní pracovat se zařízeními připojenými k vaší IoT Central aplikaci. Můžete mít například zobrazení, která zobrazují telemetrii, zobrazení, která zobrazují vlastnosti, a zobrazení, která umožňují upravit zapisovatelné a cloudové vlastnosti. Zobrazení jsou součástí šablony zařízení.

Chcete-li přidat některá výchozí zobrazení do šablony zařízení **snímače životního prostředí** , přejděte do šablony zařízení, vyberte možnost **zobrazení**a vyberte dlaždici **Generovat výchozí zobrazení** . Ujistěte se, že je **zapnutá**možnost **Přehled** a **informace o nástroji** , a pak vyberte **Generovat výchozí zobrazení řídicích panelů**. Teď máte ve vaší šabloně definovaná dvě výchozí zobrazení.

Rozhraní **snímače prostředí** zahrnuje dvě zapisovatelné vlastnosti – **jméno zákazníka** a **úroveň jasu**. Chcete-li vytvořit zobrazení, můžete použít k úpravě těchto vlastností:

1. Vyberte **zobrazení** a pak vyberte dlaždici pro **úpravu zařízení a dat v cloudu** .

1. Jako název formuláře zadejte _vlastnosti_ .

1. Vyberte vlastnosti **úrovně jasu** a **název zákazníka** . Pak vyberte **přidat oddíl**.

1. Uložte provedené změny.

![Přidání zobrazení pro povolení úprav vlastností](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>Publikování šablony

Před přidáním zařízení do aplikace IoT Central, která používá šablonu zařízení **snímače prostředí** , je nutné šablonu publikovat.

V šabloně zařízení vyberte **publikovat**. Na panelu, který zobrazuje změny k publikování, vyberte **publikovat**.

Chcete-li ověřit, zda je šablona připravena k použití, přejděte na stránku **zařízení** v aplikaci IoT Central. V části **zařízení** se zobrazuje seznam publikovaných zařízení v aplikaci:

![Publikované šablony na stránce zařízení](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidejte reálné zařízení do šablony zařízení, kterou jste vytvořili v předchozí části:

1. Na stránce **zařízení** vyberte šablonu zařízení **snímače životního prostředí** .

1. Vyberte **+ Nový**.

1. Ujistěte se, **že je** **simulovaná** . Potom vyberte **Vytvořit**.

Klikněte na název zařízení a pak vyberte **připojit**. Poznamenejte si informace o připojení zařízení na stránce **připojení zařízení** – **Rozsah ID**, **ID zařízení**a **primární klíč**. Tyto hodnoty budete potřebovat při vytváření kódu zařízení:

![Informace o připojení zařízení](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Následující kroky ukazují, jak vytvořit klientskou aplikaci Node. js, která implementuje reálné zařízení, které jste přidali do aplikace. Tato aplikace Node. js simuluje chování reálného zařízení.

1. V prostředí příkazového řádku přejděte do složky `environmental-sensor`, kterou jste předtím vytvořili.

1. Chcete-li inicializovat projekt Node. js a nainstalovat požadované závislosti, spusťte následující příkazy – při spuštění `npm init`přijměte všechny výchozí možnosti:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Ve složce `environmental-sensor` vytvořte soubor s názvem **environmentalSensor. js** .

1. Na začátek souboru **environmentalSensor. js** přidejte následující příkazy `require`:

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

    Aktualizujte zástupné symboly `{your Scope ID}`, `{your Device ID}`a `{your Primary Key}` s hodnotami, které jste si poznamenali dříve. V této ukázce inicializujete `targetTemperature` na nulu, můžete použít aktuální čtení ze zařízení nebo hodnotu z vlákna zařízení.

1. K odeslání telemetrie do aplikace Azure IoT Central přidejte do souboru následující funkci:

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

1. K odeslání vlastností zařízení do aplikace Azure IoT Central přidejte do souboru následující funkci:

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

1. Přidejte následující kód, který bude zpracovávat příkazy odesílané z aplikace IoT Central:

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

## <a name="run-your-nodejs-application"></a>Spuštění aplikace Node. js

Pokud chcete spustit klientskou aplikaci pro zařízení, spusťte v prostředí příkazového řádku následující příkaz:

```cmd/sh
node environmentalSensor.js
```

Můžete vidět, že se zařízení připojí k aplikaci Azure IoT Central a začne odesílat telemetrii:

![Spuštění klientské aplikace](media/tutorial-connect-device/run-application.png)

Jako operátor ve vaší aplikaci Azure IoT Central můžete:

* Zobrazení telemetrie odesílané zařízením na stránce **Přehled** :

    ![Zobrazení telemetrických dat](media/tutorial-connect-device/view-telemetry.png)

* Aktualizovat hodnoty zapisovatelné vlastnosti na stránce **vlastností** :

    ![Aktualizovat vlastnosti](media/tutorial-connect-device/update-properties.png)

    ![Aktualizovat vlastnosti zařízení](media/tutorial-connect-device/update-properties-device.png)

* Zavolejte příkazy ze stránky **příkazy** :

    ![Příkaz pro blikání volání](media/tutorial-connect-device/call-command.png)

    ![Volat příkazové zařízení pro blikání](media/tutorial-connect-device/call-command-device.png)

* Zobrazení vlastností zařízení na stránce **o produktu** :

    ![Zobrazení vlastností](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o modelech schopností zařízení a o tom, jak vytvářet vlastní šablony zařízení, přejděte k Průvodci postupy:

> [!div class="nextstepaction"]
> [Definovat nový typ zařízení IoT](./howto-set-up-template.md)
