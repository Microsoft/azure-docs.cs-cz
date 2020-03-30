---
title: Připojení Raspberry Pi k řešení vzdáleného monitorování – Node.js – Azure | Dokumenty společnosti Microsoft
description: Popisuje, jak připojit zařízení Raspberry Pi k akcelerátoru řešení vzdáleného monitorování pomocí aplikace napsané v Souboru Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: 98d947e8aabf20fbfdb192cb80c9bc881007d5da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889273"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Připojení zařízení Raspberry Pi k akcelerátoru řešení vzdáleného monitorování (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se můžete připojit skutečné zařízení k akcelerátoru řešení vzdáleného monitorování. V tomto kurzu použijete Node.js, což je dobrá volba pro prostředí s minimálními omezeními prostředků.

Pokud dáváte přednost simulaci zařízení, přečtěte si informace [o vytvoření a testování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Požadovaný hardware

Stolní počítač, který vám umožní vzdáleně se připojit k příkazovému řádku na Raspberry Pi.

[Microsoft IoT Starter Kit pro Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) nebo ekvivalentní komponenty. Tento kurz používá následující položky ze sady:

- Malina Pi 3
- MicroSD karta (s NOOBS)
- Kabel USB Mini
- Ethernetový kabel

### <a name="required-desktop-software"></a>Požadovaný software pro stolní počítače

Potřebujete klienta SSH na vašem stolním počítači, abyste mohli vzdáleně přistupovat k příkazovému řádku na Raspberry Pi.

- Systém Windows neobsahuje klienta SSH. Doporučujeme používat [PuTTY](https://www.putty.org/).
- Většina linuxových distribucí a Mac OS obsahuje nástroj SSH příkazového řádku. Další informace naleznete v tématu [SSH Using Linux nebo Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Požadovaný software Raspberry Pi

Pokud jste tak ještě neučinili, nainstalujte na Raspberry Pi soubor Node.js verze 4.0.0 nebo novější. Následující kroky ukazují, jak nainstalovat Soubor Node.js v6 na raspberry pi:

1. Připojte se k Raspberry Pi pomocí `ssh`. Další informace naleznete v tématu [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) na [webových stránkách Raspberry Pi](https://www.raspberrypi.org/).

1. Pomocí následujícího příkazu aktualizujte Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Pomocí následujících příkazů odeberte ze raspberry pi všechny existující instalace souboru Node.js:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Ke stažení a instalaci souboru Node.js v6 na raspberry pi použijte následující příkaz:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. Pomocí následujícího příkazu ověřte, zda jste úspěšně nainstalovali soubor Node.js v6.11.4:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Vytvoření řešení Node.js

Pomocí `ssh` připojení k Raspberry Pi proveďte následující kroky:

1. Vytvořte složku `remotemonitoring` s názvem ve vaší domovské složce na Raspberry Pi. Přejděte do této složky v příkazovém řádku:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Chcete-li stáhnout a nainstalovat balíčky, které potřebujete k dokončení ukázkové aplikace, spusťte následující příkazy:

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. Ve `remotemonitoring` složce vytvořte soubor s názvem **remote_monitoring.js**. Otevřete tento soubor v textovém editoru. Na Raspberry Pi můžete použít `nano` `vi` textové editory nebo textové editory.

1. Do souboru **remote_monitoring.js** přidejte následující `require` příkazy:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Přidejte následující deklarace proměnných za příkazy `require`. Nahraďte zástupnou hodnotu `{device connection string}` hodnotou, kterou jste si poznamenali pro zařízení, které jste zřídit v řešení vzdáleného monitorování:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Chcete-li definovat některá základní telemetrická data, přidejte následující proměnné:

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

1. Přidejte následující proměnnou k definování ohlášených vlastností, které mají být odeslány do řešení. Tyto vlastnosti zahrnují metadata, která se mají zobrazit ve webovém uživatelském rozhraní:

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

1. Přidejte následující pomocnou funkci, která se použije k náhodnému použití hodnot telemetrie:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Přidejte následující obecnou funkci pro zpracování přímých volání metod z řešení. Funkce zobrazí informace o přímé metodě, která byla vyvolána, ale v této ukázce žádným způsobem neupravuje zařízení. Řešení používá přímé metody k jednání na zařízeních:

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

1. Přidejte následující funkci pro zpracování volání přímé metody **FirmwareUpdate** z řešení. Funkce ověří parametry předané v přímé datové části metody a poté asynchronně spustí simulaci aktualizace firmwaru:

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

1. Přidejte následující funkci pro simulaci dlouhotrvajícího toku aktualizace firmwaru, který hlásí průběh zpět do řešení:

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

1. Přidejte následující kód pro odesílání telemetrických dat do řešení. Klientská aplikace přidá do zprávy vlastnosti k identifikaci schématu zprávy:

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

1. Do této oblasti přidejte následující kód:

    * Otevřete připojení.
    * Nastavte obslužnou rutinu pro požadované vlastnosti.
    * Odeslat ohlášené vlastnosti.
    * Zaregistrujte obslužné rutiny pro přímé metody. Ukázka používá samostatnou obslužnou rutinu pro metodu přímé aktualizace firmwaru.
    * Začněte posílat telemetrii.

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

1. Uložte změny do souboru **remote_monitoring.js.**

1. Chcete-li spustit ukázkovou aplikaci, spusťte na příkazovém řádku na Raspberry Pi následující příkaz:

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
