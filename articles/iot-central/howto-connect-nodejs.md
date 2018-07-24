---
title: Připojit obecný klientská aplikace Node.js do Azure IoT Central | Dokumentace Microsoftu
description: Jako vývojář zařízení jak se připojit k aplikaci Azure IoT Central Obecné zařízení Node.js.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 55ce85702804d99d806220d7f0a4ea0820975f4f
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206033"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Připojení aplikace obecného klienta aplikace Azure IoT Central (Node.js)

Tento článek popisuje, jak jako vývojář zařízení připojit Obecná aplikace Node.js představující fyzické zařízení na aplikaci Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

1. Aplikace Azure IoT Central. Další informace najdete v tématu [vytvoření aplikace Azure IoT Central](howto-create-application.md).
1. Vývojovém počítači s [Node.js](https://nodejs.org/) verze 4.0.0 nebo novější. Můžete spustit `node --version` na příkazovém řádku k ověření verze. Node.js je k dispozici pro širokou škálu operačních systémů.

## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

V aplikaci Azure IoT Central je třeba zařízení šablona s následující měření a definovány vlastnosti zařízení:

### <a name="telemetry-measurements"></a>Měření telemetrie

Přidejte následující telemetrii v **měření** stránky:

| Zobrazovaný název | Název pole  | Jednotky | Minimum | Maximum | Desetinná místa |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Teplota  | teplota | F     | 60  | 110 | 0              |
| Vlhkost     | vlhkost    | %     | 0   | 100 | 0              |
| Tlak     | tlak    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  Datový typ telemetrických dat měření je double.

Názvy polí zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí nejsou shodné, nelze zobrazit telemetrická data v aplikaci.

### <a name="state-measurements"></a>Měření stavu

Přidat následující stav v **měření** stránky:

| Zobrazovaný název | Název pole  | Hodnota 1 | Zobrazovaný název | Hodnota 2 | Zobrazovaný název |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Režim ventilátoru     | fanmode     | 1       | Spuštěno      | 0       | Zastaveno      |

> [!NOTE]
  Datový typ měření stavu je řetězec.

Názvy polí zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí nejsou shodné, stav nelze zobrazit v aplikaci.

### <a name="event-measurements"></a>Měření událostí

Přidejte následující událost v **měření** stránky:

| Zobrazovaný název | Název pole  | Severity |
| ------------ | ----------- | -------- |
| Přehřívání  | overheat    | Chyba    |

> [!NOTE]
  Datový typ měření událostí je řetězec.

### <a name="device-properties"></a>Vlastnosti zařízení

Přidejte následující vlastnosti zařízení v **stránku vlastností**:

| Zobrazovaný název        | Název pole        | Typ dat |
| ------------------- | ----------------- | --------- |
| Sériové číslo       | serialNumber      | text      |
| Výrobce zařízení | výrobce      | text      |

Názvy polí zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí nejsou shodné, aplikace nejde zobrazit hodnotu vlastnosti.

### <a name="settings"></a>Nastavení

Přidejte následující **číslo** nastavení **stránka nastavení**:

| Zobrazovaný název    | Název pole     | Jednotky | Desetinná místa | Minimum | Maximum  | Počáteční |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Ventilátor rychlost       | fanSpeed       | ot. / min   | 0        | 0   | 3000 | 0       |
| Nastavená teplota | setTemperature | F     | 0        | 20  | 200  | 80      |

Pole Název zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí nejsou shodné, zařízení nelze získat hodnota nastavení.

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidání skutečné zařízení ze šablony zařízení můžete vytvořit a poznamenejte si připojovací řetězec zařízení. Další informace najdete v tématu [skutečné zařízení přidat do aplikace Azure IoT Central](tutorial-add-device.md)

### <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Následující kroky ukazují, jak vytvořit klientskou aplikaci, která implementuje skutečné zařízení, které jste přidali do aplikace.

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

    Aktualizujte zástupný text `{your device connection string}` vaším připojovacím řetězcem zařízení. Tuto hodnotu na stránce podrobností připojení kopírovat, pokud jste přidali skutečné zařízení. V této ukázce jsme inicializovat `targetTemperature` na nulu, můžete volitelně provést aktuální čtení ze zařízení nebo hodnotu z dvojčete zařízení. 

1. K odesílání telemetrických dat, stavu a událostí měření do aplikace Azure IoT Central, přidejte následující funkci k souboru:

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
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
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

1. Přidejte následující text do dokončete připojení ke službě Azure IoT Central a propojení funkcí v klientském kódu:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
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

* Zobrazit hodnoty vlastností zařízení odeslané ze zařízení **vlastnosti** stránky.

    ![Zobrazení vlastností zařízení](media/howto-connect-nodejs/viewproperties.png)

* Nastavte ventilátor rychlost a cíl teploty od **nastavení** stránky.

    ![Ventilátor rychlost set](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit obecného klienta Node.js do aplikace Azure IoT Central, tady jsou další navrhované kroky:
* [Příprava a připojte Raspberry Pi](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
