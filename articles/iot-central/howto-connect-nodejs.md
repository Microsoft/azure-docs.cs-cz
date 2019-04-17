---
title: Připojit obecný klientská aplikace Node.js do Azure IoT Central | Dokumentace Microsoftu
description: Jako vývojář zařízení jak se připojit k aplikaci Azure IoT Central Obecné zařízení Node.js.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 5497e4956fbdc74eced302867c33a66d07d6a184
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617933"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Připojení aplikace obecného klienta aplikace Azure IoT Central (Node.js)

Tento článek popisuje, jak jako vývojář zařízení připojit Obecná aplikace Node.js představující skutečné zařízení do aplikace Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

1. Aplikace Azure IoT Central. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
1. Vývojovém počítači s [Node.js](https://nodejs.org/) verze 4.0.0 nebo novější. Můžete spustit `node --version` na příkazovém řádku k ověření verze. Node.js je k dispozici pro širokou škálu operačních systémů.

## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

V aplikaci Azure IoT Central je třeba šablona zařízení s následujícími měření, vlastnosti zařízení, nastavení a příkazy:

### <a name="telemetry-measurements"></a>Měření telemetrie

Přidejte následující telemetrii na **měření** stránky:

| Zobrazovaný název | Název pole  | Jednotky | Minimum | Maximum | Desetinná místa |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Teplota  | teplota | F     | 60  | 110 | 0              |
| Vlhkost     | vlhkost    | %     | 0   | 100 | 0              |
| Tlak     | tlak    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> Datový typ telemetrických dat měření je plovoucí číslo bodu.

Názvy polí zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí neshodují názvy vlastností v odpovídajícím kódu zařízení, nelze zobrazit telemetrická data v aplikaci.

### <a name="state-measurements"></a>Měření stavu

Přidejte následující stav na **měření** stránky:

| Zobrazovaný název | Název pole  | Hodnota 1 | Zobrazovaný název | Hodnota 2 | Zobrazovaný název |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Režim ventilátoru     | fanmode     | 1       | Spuštěno      | 0       | Zastaveno      |

> [!NOTE]
> Datový typ měření stavu je řetězec.

Názvy polí zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí neshodují názvy vlastností v odpovídajícím kódu zařízení, stavu nelze zobrazit v aplikaci.

### <a name="event-measurements"></a>Měření událostí

Přidejte následující událost na **měření** stránky:

| Zobrazovaný název | Název pole  | Severity |
| ------------ | ----------- | -------- |
| Přehřívání  | overheat    | Chyba    |

> [!NOTE]
> Datový typ měření událostí je řetězec.

### <a name="device-properties"></a>Vlastnosti zařízení

Přidejte následující vlastnosti zařízení na **vlastnosti** stránky:

| Zobrazovaný název        | Název pole        | Typ dat |
| ------------------- | ----------------- | --------- |
| Sériové číslo       | serialNumber      | text      |
| Výrobce zařízení | výrobce      | text      |

Názvy polí zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí neshodují názvy vlastností v odpovídajícím kódu zařízení, nelze zobrazit vlastnosti v aplikaci.

### <a name="settings"></a>Nastavení

Přidejte následující **číslo** nastavení **nastavení** stránky:

| Zobrazovaný název    | Název pole     | Jednotky | Desetinná místa | Minimum | Maximum  | Počáteční |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Ventilátor rychlost       | fanSpeed       | ot. / min   | 0        | 0   | 3000 | 0       |
| Nastavená teplota | setTemperature | F     | 0        | 20  | 200  | 80      |

Pole Název zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí neshodují názvy vlastností v odpovídajícím kódu zařízení, zařízení nelze získat hodnotu nastavení.

### <a name="commands"></a>Příkazy

Přidáním následujícího příkazu na **příkazy** stránky:

| Zobrazovaný název    | Název pole     | Výchozí časový limit | Typ dat |
| --------------- | -------------- | --------------- | --------- |
| Odpočítávání       | Odpočítávání      | 30              | číslo    |

Přidejte následující vstupní pole odpočítávání příkazu:

| Zobrazovaný název    | Název pole     | Typ dat | Hodnota |
| --------------- | -------------- | --------- | ----- |
| Od      | countFrom      | číslo    | 10    |

Názvy polí zadejte přesně tak, jak je znázorněno v tabulkách do šablony zařízení. Pokud se názvy polí neshodují názvy vlastností v odpovídajícím kódu zařízení, zařízení nemůže zpracovat příkaz.

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central skutečné zařízení můžete přidáte šablonu zařízení, které jste vytvořili v předchozí části.

Postupujte podle pokynů v kurzu "Přidat zařízení", který [generovat připojovací řetězec pro skutečné zařízení](tutorial-add-device.md#generate-connection-string). Použít tento připojovací řetězec v následující části:

### <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Následující kroky ukazují, jak vytvořit klientskou aplikaci, která implementuje skutečné zařízení, které jste přidali do aplikace. Aplikace Node.js zde představuje skutečné zařízení. 

1. Na počítači vytvořte složku s názvem `connected-air-conditioner-adv`. Přejděte do této složky ve vašem prostředí příkazového řádku.

1. Inicializace projektu Node.js, spusťte následující příkazy:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Vytvořte soubor s názvem **connectedAirConditionerAdv.js** v `connected-air-conditioner-adv` složky.

1. Přidejte následující `require` příkazy na začátku **connectedAirConditionerAdv.js** souboru:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
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

    Aktualizujte zástupný text `{your device connection string}` s [připojovací řetězec zařízení](tutorial-add-device.md#generate-connection-string). V této ukázce je inicializovat `targetTemperature` na nulu, můžete použít aktuální čtení ze zařízení nebo hodnotu z dvojčete zařízení.

1. K odesílání telemetrických dat, stavu a měření událostí do aplikace Azure IoT Central, přidejte následující funkci k souboru:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Vlastnosti zařízení odesílat do aplikace Azure IoT Central, do souboru přidejte následující funkci:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Chcete-li definovat nastavení, která odpovídá vaše zařízení, přidejte následující definice:

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

1. Pro zpracování aktualizované nastavení aplikace Azure IoT Central, přidejte do souboru následující:

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

1. Přidejte následující kód pro zpracování příkazu odpočítávání odeslané z aplikace IoT Central:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
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
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
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

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Spustit aplikaci v Node.js

V prostředí příkazového řádku spusťte následující příkaz:

```cmd/sh
node connectedAirConditionerAdv.js
```

Jako operátor v aplikaci Azure IoT Central pro skutečné zařízení můžete:

* Zobrazení telemetrických dat na **měření** stránky:

    ![Zobrazení telemetrických dat](media/howto-connect-nodejs/viewtelemetry.png)

* Zobrazit hodnoty vlastností zařízení odeslané ze zařízení **vlastnosti** stránky. Vlastnosti dlaždice aktualizace zařízení při připojení zařízení:

    ![Zobrazení vlastností zařízení](media/howto-connect-nodejs/viewproperties.png)

* Nastavte ventilátor rychlost a cíl teploty od **nastavení** stránky:

    ![Ventilátor rychlost set](media/howto-connect-nodejs/setfanspeed.png)

* Volání příkazu odpočítávání z **příkazy** stránky:

    ![Volání příkazu odpočítávání](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak se připojit k aplikaci Azure IoT Central obecného klienta Node.js, navrhované dalším krokem je další způsob [nastavit šablonu vlastního zařízení](howto-set-up-template.md) pro zařízení IoT.
