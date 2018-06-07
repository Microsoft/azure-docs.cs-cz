---
title: Připojit obecné klienta aplikace Node.js ve službě Azure IoT centrální | Microsoft Docs
description: Jako vývojář zařízení jak se připojit k aplikaci Azure IoT centrální Obecné zařízení Node.js.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 42ede975f2cfde2d9c0a61d15ba1af412a88c556
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628534"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Připojit obecné klientskou aplikaci, aby vaše aplikace Azure IoT centrální (Node.js)

Tento článek popisuje, jak jako vývojář zařízení připojit Obecná aplikace Node.js do aplikace Microsoft Azure IoT centrální představující fyzického zařízení.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

1. Azure IoT centrální aplikaci. Další informace najdete v tématu [vytvořit aplikaci Azure IoT centrální](howto-create-application.md).
1. Vývoj pro počítač s [Node.js](https://nodejs.org/) verze 4.0.0 nainstalovaný nebo novější. Můžete spustit `node --version` v příkazovém řádku zkontrolujte vaši verzi. Node.js je k dispozici pro širokou škálu operačních systémů.

V aplikaci Azure IoT centrální potřebujete šablonu zařízení s následující měření a definované vlastnosti zařízení:

### <a name="telemetry-measurements"></a>Měření telemetrie

Přidejte následující telemetrie **měření** stránky:

| Zobrazovaný název | Název pole  | Jednotky | Minimum | Maximum | Desetinných míst |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Teplota  | Teplota | F     | 60  | 110 | 0              |
| Vlhkost     | vlhkosti    | %     | 0   | 100 | 0              |
| Tlak     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  Datový typ měření telemetrie je double.

Názvy polí zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí neshodují, nelze zobrazit telemetrii, v aplikaci.

### <a name="state-measurements"></a>Měření stavu

Přidejte následující stav v **měření** stránky:

| Zobrazovaný název | Název pole  | Hodnota 1 | Zobrazovaný název | Hodnota 2 | Zobrazovaný název |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Režim ventilátoru     | fanmode     | 1       | Spuštěno      | 0       | Zastaveno      |

> [!NOTE]
  Datový typ měření stavu je řetězec.

Názvy polí zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí neshodují, stav nelze zobrazit v aplikaci.

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

Názvy polí zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí neshodují, aplikace nejde zobrazit hodnotu vlastnosti.

### <a name="settings"></a>Nastavení

Přidejte následující **číslo** nastavení v **stránka nastavení**:

| Zobrazovaný název    | Název pole     | Jednotky | Počet desetinných míst | Minimum | Maximum  | Počáteční |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Ventilátor rychlosti       | fanSpeed       | ot. / min   | 0        | 0   | 3000 | 0       |
| Nastavená teplota | setTemperature | F     | 0        | 20  | 200  | 80      |

Název pole zadejte přesně tak, jak je znázorněno v tabulce do šablony zařízení. Pokud se názvy polí neshodují, zařízení nelze získat hodnotu nastavení.

### <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT centrální přidáte skutečné zařízení ze zařízení šablony můžete vytvořit a poznamenejte si připojovací řetězec zařízení. Další informace najdete v tématu [přidat skutečné zařízení Azure IoT centrální aplikace](tutorial-add-device.md)

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Následující kroky ukazují, jak vytvořit klientskou aplikaci, která implementuje skutečné zařízení, které jste přidali do aplikace.

1. Na počítači vytvořte složku s názvem `connected-air-conditioner-adv`. Přejděte do této složky ve vašem prostředí příkazového řádku.

1. K chybě při inicializaci projekt Node.js, spusťte následující příkazy:

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

    Aktualizujte zástupný symbol `{your device connection string}` připojovacím řetězcem zařízení. Tato hodnota na stránce podrobností připojení zkopírovat přidali skutečné zařízení. V této ukázce jsme inicializovat `targetTemperature` na nulu, můžete volitelně provést aktuální při čtení ze zařízení nebo hodnota z dvojče zařízení. 

1. Pokud chcete odeslat do aplikace Azure IoT centrální měření telemetrických dat, stavu a událostí, přidejte do souboru následující funkce:

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

    1. Pokud chcete odeslat do aplikace Azure IoT centrální vlastnosti zařízení, do souboru přidejte následující funkce:

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

1. K definování nastavení, které zařízení reaguje na, přidejte následující definice:

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

1. Pro zpracování aktualizované nastavení z vaší aplikace Azure IoT centrální, přidejte do souboru následující:

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

1. Přidejte následující příkaz a dokončete připojení ke službě Azure IoT centrální a propojte funkce v klientském kódu:

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

## <a name="run-your-nodejs-application"></a>Spusťte aplikaci Node.js

V prostředí příkazového řádku spusťte následující příkaz:

```cmd/sh
node connectedAirConditionerAdv.js
```

Jako operátor v aplikaci Azure IoT centrální pro skutečné zařízení můžete:

* Zobrazit telemetrii na **měření** stránky:

    ![Zobrazení telemetrických dat](media/howto-connect-nodejs/viewtelemetry.png)

* Zobrazit hodnoty vlastností zařízení odeslané ze zařízení **vlastnosti** stránky.

    ![Zobrazení vlastností zařízení](media/howto-connect-nodejs/viewproperties.png)

* Nastavit ventilátor rychlost a cíle teploty z **nastavení** stránky.

    ![Ventilátor rychlost sady](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili jak připojit obecné Node.js klienta aplikace Azure IoT centrální, tady jsou navrhované další kroky:
* [Příprava a připojení Raspberry Pi](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
