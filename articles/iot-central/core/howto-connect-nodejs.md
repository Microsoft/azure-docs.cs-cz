---
title: Připojení klientské aplikace v Node. js k Azure IoT Central | Microsoft Docs
description: Jako vývojář zařízení, jak připojit obecné zařízení Node. js k vaší aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 09/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1ddb947e9bcbdde655b5c83cffb67e8fc13d9d0e
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048680"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Připojení Obecné klientské aplikace k aplikaci Azure IoT Central (Node. js)

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Tento článek popisuje, jak jako vývojář zařízení připojit obecnou aplikaci Node. js představující reálné zařízení k vaší aplikaci Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

- Aplikace Azure IoT Central. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
- Vývojový počítač s nainstalovanou aplikací [Node. js](https://nodejs.org/) verze 4.0.0 nebo novější. Můžete spustit `node --version` na příkazovém řádku a ověřit svou verzi. Node.js je k dispozici pro širokou škálu operačních systémů.

## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

V aplikaci Azure IoT Central budete potřebovat šablonu zařízení s následujícími měřeními, vlastnostmi zařízení, nastaveními a příkazy.

Další informace o platných názvech vlastností naleznete v tématu [Formát značek a vlastností](../../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

### <a name="telemetry-measurements"></a>Měření telemetrie

Přidejte na stránku **měření** následující telemetrii:

| Zobrazovaný název | Název pole  | Jednotky | Minimum | Maximum | Desetinná místa |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Teplota  | Teplota | F     | 60  | 110 | 0              |
| Vlhkost     | vlhkost    | %     | 0   | 100 | 0              |
| Citlivost     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> Datový typ měření telemetrie je číslo s plovoucí desetinnou čárkou.

Zadejte názvy polí přesně tak, jak jsou uvedeny v tabulce, do šablony zařízení. Pokud názvy polí neodpovídají názvům vlastností v odpovídajícím kódu zařízení, telemetrie nejde v aplikaci zobrazit.

### <a name="state-measurements"></a>Měření stavu

Na stránce **měření** přidejte následující stav:

| Zobrazovaný název | Název pole  | Hodnota 1 | Zobrazovaný název | Hodnota 2 | Zobrazovaný název |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Režim ventilátoru     | fanmode     | 1       | Běží      | 0       | Zastaveno      |

> [!NOTE]
> Datový typ měření stavu je řetězec.

Zadejte názvy polí přesně tak, jak jsou uvedeny v tabulce, do šablony zařízení. Pokud názvy polí neodpovídají názvům vlastností v odpovídajícím kódu zařízení, stav nelze zobrazit v aplikaci.

### <a name="event-measurements"></a>Měření událostí

Na stránce **měření** přidejte následující událost:

| Zobrazovaný název | Název pole  | Severity |
| ------------ | ----------- | -------- |
| Přehřívání  | overheat    | Chyba    |

> [!NOTE]
> Datový typ měření události je řetězec.

### <a name="location-measurements"></a>Měření polohy

Na stránce **měření** přidejte následující měření umístění:

| Zobrazovaný název | Název pole  |
| ------------ | ----------- |
| Umístění     | location    |

Datový typ měření umístění se skládá ze dvou čísel s plovoucí desetinnou čárkou pro zeměpisnou šířku a zeměpisnou šířku a volitelné číslo s plovoucí desetinnou čárkou pro nadmořskou výšku.

Zadejte názvy polí přesně tak, jak jsou uvedeny v tabulce, do šablony zařízení. Pokud názvy polí neodpovídají názvům vlastností v odpovídajícím kódu zařízení, umístění nelze v aplikaci zobrazit.

### <a name="device-properties"></a>Vlastnosti zařízení

Na stránce **vlastnosti** přidejte následující vlastnosti zařízení:

| Zobrazovaný název        | Název pole        | Data type |
| ------------------- | ----------------- | --------- |
| Sériové číslo       | serialNumber      | text      |
| Výrobce zařízení | výrobců      | text      |

Zadejte názvy polí přesně tak, jak je uvedeno v tabulce, do šablony zařízení. Pokud názvy polí neodpovídají názvům vlastností v odpovídajícím kódu zařízení, vlastnosti nelze zobrazit v aplikaci.

### <a name="settings"></a>Nastavení

Na stránce **Nastavení** přidejte následující **číslo** nastavení:

| Zobrazovaný název    | Název pole     | Jednotky | desetinných míst | Minimum | Maximum  | Pořizovací |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Rychlost ventilátoru       | fanSpeed       | /min   | 0        | 0   | 3000 | 0       |
| Nastavená teplota | setTemperature | F     | 0        | 20  | 200  | 80      |

Zadejte název pole přesně tak, jak je uvedeno v tabulce v šabloně zařízení. Pokud názvy polí neodpovídají názvům vlastností v odpovídajícím kódu zařízení, zařízení nemůže přijmout hodnotu nastavení.

### <a name="commands"></a>Příkazy

Na stránce **příkazy** přidejte následující příkaz:

| Zobrazovaný název    | Název pole     | Výchozí časový limit | Typ dat |
| --------------- | -------------- | --------------- | --------- |
| Odpočítávání       | Odpočítávání      | 30              | číslo    |

Do příkazu odpočítávání přidejte následující vstupní pole:

| Zobrazovaný název    | Název pole     | Typ dat | Hodnota |
| --------------- | -------------- | --------- | ----- |
| Počet z      | countFrom      | číslo    | 10    |

Zadejte názvy polí přesně tak, jak jsou uvedeny v tabulkách do šablony zařízení. Pokud názvy polí neodpovídají názvům vlastností v odpovídajícím kódu zařízení, zařízení nemůže tento příkaz zpracovat.

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidejte reálné zařízení do šablony zařízení, kterou jste vytvořili v předchozí části.

Poznamenejte si informace o připojení zařízení na stránce **připojení zařízení** : **ID oboru**, **ID zařízení**a **primární klíč**. Tyto hodnoty můžete do kódu zařízení přidat později v tomto průvodci:

![Informace o připojení zařízení](./media/howto-connect-nodejs/device-connection.png)

### <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Následující kroky ukazují, jak vytvořit klientskou aplikaci, která implementuje reálné zařízení, které jste přidali do aplikace. V tomto případě aplikace Node. js představuje reálné zařízení.

1. Na počítači vytvořte složku s názvem `connected-air-conditioner-adv`. V prostředí příkazového řádku přejděte do této složky.

1. Chcete-li inicializovat projekt Node. js, spusťte následující příkazy:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Ve složce `connected-air-conditioner-adv` vytvořte soubor s názvem **connectedAirConditionerAdv. js** .

1. Na začátek souboru **connectedAirConditionerAdv. js** přidejte následující příkazy `require`:

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
    var locLong = -122.1215;
    var locLat = 47.6740;
    ```

    Aktualizujte zástupné symboly `{your Scope ID}`, `{your Device ID}`a `{your Primary Key}` s hodnotami, které jste si poznamenali dříve. V této ukázce inicializujete `targetTemperature` na nulu, můžete použít aktuální čtení ze zařízení nebo hodnotu z vlákna zařízení.

1. Pokud chcete do aplikace Azure IoT Central odeslat měření telemetrie, stavu, události a umístění, přidejte do souboru následující funkci:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
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

1. Pokud chcete definovat nastavení, na které zařízení reaguje, přidejte následující definici:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
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

1. Pokud chcete zpracovat aktualizované nastavení z aplikace IoT Central Azure, přidejte do souboru následující:

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

1. Přidejte následující kód pro zpracování příkazu odpočítávání odesílaného z aplikace IoT Central:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');
    
      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;
    
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          hubClient.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }
    
            doCountdown();
          });
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

        // Create handler for countdown command
        hubClient.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
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

Spusťte následující příkaz v prostředí příkazového řádku:

```cmd/sh
node connectedAirConditionerAdv.js
```

Jako operátor ve vaší aplikaci Azure IoT Central můžete pro vaše reálné zařízení:

* Zobrazit telemetrii na stránce **měření** :

    ![Zobrazení telemetrických dat](media/howto-connect-nodejs/viewtelemetry.png)

* Zobrazení umístění na stránce **měření** :

    ![Zobrazit měření polohy](media/howto-connect-nodejs/viewlocation.png)

* Zobrazení hodnot vlastností zařízení odeslaných ze zařízení na stránce **vlastností** . Po připojení zařízení se aktualizuje dlaždice vlastnosti zařízení:

    ![Zobrazit vlastnosti zařízení](media/howto-connect-nodejs/viewproperties.png)

* Nastavte rychlost ventilátoru a cílovou teplotu ze stránky **Nastavení** :

    ![Nastavit rychlost ventilátoru](media/howto-connect-nodejs/setfanspeed.png)

* Na stránce **příkazy** zavolejte příkaz pro odpočítávání:

    ![Příkaz odpočítávání volání](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s tím, jak připojit obecného klienta Node. js k vaší aplikaci Azure IoT Central, je doporučený další krok, kde se dozvíte, jak [nastavit vlastní šablonu zařízení](howto-set-up-template.md) pro vlastní zařízení IoT.
