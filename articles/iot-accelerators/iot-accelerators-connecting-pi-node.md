---
title: Připojení k řešení vzdáleného monitorování z malinu PI – Node.js – Azure | Microsoft Docs
description: V této části najdete popis postupu připojení zařízení malinu pi k akcelerátoru řešení vzdáleného monitorování pomocí aplikace napsané v Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 46f8f811e7d1a019fa332cd289892d110531c84d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261545"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Připojení zařízení malinu pi k akcelerátoru řešení vzdáleného monitorování (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak připojit reálné zařízení k akcelerátoru řešení vzdáleného monitorování. V tomto kurzu použijete Node.js, což je dobrá možnost pro prostředí s minimálními omezeními prostředků.

Pokud dáváte přednost simulaci zařízení, přečtěte si téma [Vytvoření a otestování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Požadovaný hardware

Stolní počítač, který vám umožní vzdálené připojení k příkazovému řádku v malin. PI.

[Sada Microsoft IoT Starter Kit pro malinu PI 3](https://azure.microsoft.com/develop/iot/starter-kits/) nebo ekvivalentní součásti. Tento kurz používá následující položky ze sady:

- Malina Pi 3
- Karta MicroSD (s NOOBS)
- Mini kabel USB
- Kabel Ethernet

### <a name="required-desktop-software"></a>Požadovaný desktopový software

Na stolním počítači budete potřebovat klienta SSH, aby bylo možné vzdáleně přistupovat k příkazovému řádku v malinu PI.

- Systém Windows nezahrnuje klienta SSH. Doporučujeme použít k [disvýstupu](https://www.putty.org/).
- Většina distribucí a Mac OS systému Linux zahrnuje nástroj SSH nástroje příkazového řádku. Další informace najdete v tématu [SSH pomocí systému Linux nebo Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Požadovaný software maliny PI

Pokud jste to ještě neudělali, nainstalujte Node.js verze 4.0.0 nebo novější do maliny PI. Následující kroky ukazují, jak nainstalovat Node.js v6 do malinu PI:

1. Připojte se k malin. PI pomocí `ssh` . Další informace najdete v tématu [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) na [webu maliny PI](https://www.raspberrypi.org/).

1. Pomocí následujícího příkazu aktualizujte aplikaci malin PI:

    ```sh
    sudo apt-get update
    ```

1. Pomocí následujících příkazů odeberte všechny stávající instalace Node.js ze složky malin PI:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Pomocí následujícího příkazu Stáhněte a nainstalujte Node.js v6 do malinu PI:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. Pomocí následujícího příkazu ověřte, že jste úspěšně nainstalovali Node.js v 6.11.4:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Vytvoření řešení Node.js

Pomocí `ssh` připojení k aplikaci malin PI proveďte následující kroky:

1. Vytvořte složku s názvem `remotemonitoring` v domovské složce na malinu PI. Přejděte do této složky na příkazovém řádku:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Pokud chcete stáhnout a nainstalovat balíčky, které potřebujete k dokončení ukázkové aplikace, spusťte následující příkazy:

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. Ve `remotemonitoring` složce vytvořte soubor s názvem **remote_monitoring.js**. Otevřete tento soubor v textovém editoru. V aplikaci malin PI můžete použít `nano` `vi` editory nebo text.

1. Do **remote_monitoring.js** souboru přidejte následující `require` příkazy:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Přidejte následující deklarace proměnných za příkazy `require`. Nahraďte hodnotu zástupného symbolu `{device connection string}` hodnotou, kterou jste si poznamenali u zařízení, které jste zřídili v řešení vzdáleného monitorování:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Pokud chcete definovat některá základní data telemetrie, přidejte následující proměnné:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Chcete-li definovat některé hodnoty vlastností, přidejte následující proměnné:

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Přidejte následující proměnnou k definování hlášených vlastností k odeslání do řešení. Tyto vlastnosti zahrnují metadata, která se mají zobrazit ve webovém uživatelském rozhraní:

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. Chcete-li vytisknout výsledky operace, přidejte následující pomocnou funkci:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Přidejte následující pomocnou funkci, která se použije k náhodnému použití hodnoty telemetrie:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Přidejte následující obecnou funkci pro zpracování volání přímých metod z řešení. Funkce zobrazí informace o přímé metodě, která byla vyvolána, ale v této ukázce nemění zařízení jakýmkoli způsobem. Řešení používá přímé metody pro jednání na zařízeních:

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. Přidejte následující funkci pro zpracování volání přímé metody **FirmwareUpdate** z řešení. Funkce ověří parametry předané v datové části Direct Method a poté asynchronně spustí simulaci aktualizace firmwaru:

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. Přidejte následující funkci pro simulaci dlouhotrvajícího toku aktualizace firmwaru, který oznamuje průběh zpět do řešení:

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. Přidejte následující kód, který odešle data telemetrie do řešení. Klientská aplikace přidá do zprávy vlastnosti pro identifikaci schématu zprávy:

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. Přidejte následující kód pro vytvoření instance klienta:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Přidejte následující kód do:

    * Otevřete připojení.
    * Nastavte obslužnou rutinu pro požadované vlastnosti.
    * Odeslání hlášených vlastností.
    * Zaregistrujte obslužné rutiny pro přímé metody. Ukázka používá samostatnou obslužnou rutinu pro přímou metodu aktualizace firmwaru.
    * Zahájit odesílání telemetrie

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. Uložte změny do souboru **remote_monitoring.js** .

1. Chcete-li spustit ukázkovou aplikaci, spusťte následující příkaz na příkazovém řádku na adrese malin PI:

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
