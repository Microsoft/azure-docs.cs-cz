---
title: Zřízení zařízení pro vzdálené monitorování v Node.js – Azure | Microsoft Docs
description: Popisuje, jak se připojit zařízení k používání aplikace napsané v Node.js akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: 8bd614fd7aad248612d65717fe50e04a3fc3a9e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627326"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Připojte zařízení k řešení akcelerátoru vzdálené monitorování (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak se připojit fyzického zařízení k akcelerátoru řešení vzdáleného monitorování. V tomto kurzu použijete Node.js, který je vhodný pro prostředí s minimálními prostředků omezení.

## <a name="create-a-nodejs-solution"></a>Vytvoření řešení Node.js

Ujistěte se, že [Node.js](https://nodejs.org/) verze 4.0.0 nebo novější je nainstalován na vývojovém počítači. Můžete spustit `node --version` na příkazovém řádku, které chcete zkontrolovat verzi.

1. Vytvořte složku s názvem `remotemonitoring` na vývojovém počítači. Přejděte do této složky ve vašem prostředí příkazového řádku.

1. Chcete-li stáhnout a nainstalovat balíčky, které potřebujete k dokončení ukázkové aplikace, spusťte následující příkazy:

    ```cmd/sh
    npm init
    npm install async azure-iot-device azure-iot-device-mqtt --save
    ```

1. V `remotemonitoring` složky, vytvořte soubor s názvem **remote_monitoring.js**. Otevřete tento soubor v textovém editoru.

1. V **remote_monitoring.js** soubor, přidejte následující `require` příkazy:

    ```nodejs
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Přidejte následující deklarace proměnných za příkazy `require`. Nahraďte hodnotu zástupného symbolu `{device connection string}` s hodnotou jste si poznamenali zařízení zřízené v řešení pro vzdálené monitorování:

    ```nodejs
    var connectionString = '{device connection string}';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Chcete-li definovat některé základní telemetrická data, přidejte následující proměnné:

    ```nodejs
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Chcete-li definovat některé hodnoty vlastnosti, přidejte následující proměnné:

    ```nodejs
    var temperatureSchema = 'chiller-temperature;v1';
    var humiditySchema = 'chiller-humidity;v1';
    var pressureSchema = 'chiller-pressure;v1';
    var interval = "00:00:05";
    var deviceType = "Chiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Přidejte následující proměnnou k určení hlášené vlastností k odeslání do řešení. Tyto vlastnosti obsahovat metadata k popisu metody a používá telemetrická data zařízení:

    ```nodejs
    var reportedProperties = {
      "Protocol": "MQTT",
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        "TemperatureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
          "MessageSchema": {
            "Name": temperatureSchema,
            "Format": "JSON",
            "Fields": {
              "temperature": "Double",
              "temperature_unit": "Text"
            }
          }
        },
        "HumiditySchema": {
          "Interval": interval,
          "MessageTemplate": "{\"humidity\":${humidity},\"humidity_unit\":\"${humidity_unit}\"}",
          "MessageSchema": {
            "Name": humiditySchema,
            "Format": "JSON",
            "Fields": {
              "humidity": "Double",
              "humidity_unit": "Text"
            }
          }
        },
        "PressureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"pressure\":${pressure},\"pressure_unit\":\"${pressure_unit}\"}",
          "MessageSchema": {
            "Name": pressureSchema,
            "Format": "JSON",
            "Fields": {
              "pressure": "Double",
              "pressure_unit": "Text"
            }
          }
        }
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

1. Pokud chcete vytisknout výsledky operace, přidejte následující funkce, pomocné rutiny:

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Přidejte následující pomocné funkce sloužící k náhodné telemetrie hodnoty:

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Přidejte následující obecné funkci pro zpracování volání metody přímé z řešení. Funkce zobrazí informace o metodě direct, který byl vyvolán, ale v této ukázce neupravuje zařízení žádným způsobem. Toto řešení využívá přímé metody tak, aby fungoval na zařízení:

    ```nodejs
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

1. Přidejte následující funkci pro zpracování **FirmwareUpdate** přímé volání metod z řešení. Funkce ověřuje parametrů předaných v datové části přímá metoda a poté asynchronně spustí simulaci aktualizace firmwaru:

    ```nodejs
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

1. Přidejte následující funkci k simulaci toku aktualizace firmwaru dlouho běžící, který sestavy průběhu řešení:

    ```nodejs
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

1. Přidejte následující kód do Neodesílat telemetrická data do řešení. Klientská aplikace přidá vlastnosti zprávy k identifikaci schéma zpráva:

    ```nodejs
    function sendTelemetry(data, schema) {
      if (deviceOnline) {
        var d = new Date();
        var payload = JSON.stringify(data);
        var message = new Message(payload);
        message.properties.add('$$CreationTimeUtc', d.toISOString());
        message.properties.add('$$MessageSchema', schema);
        message.properties.add('$$ContentType', 'JSON');

        console.log('Sending device message data:\n' + payload);
        client.sendEvent(message, printErrorFor('send event'));
      } else {
        console.log('Offline, not sending telemetry');
      }
    }
    ```

1. Přidejte následující kód k vytvoření instance klienta:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Přidejte následující kód do:

    * Otevření připojení.
    * Nastavte obslužnou rutinu pro požadované vlastnosti.
    * Odešlete oznámenou vlastnosti.
    * Zaregistrujte obslužné rutiny pro přímé metody. Příklad používá samostatné obslužnou rutinu pro metodu přímé aktualizace firmwaru.
    * Zahájit odesílání telemetrie.

    ```nodejs
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
        var sendTemperatureInterval = setInterval(function () {
          temperature += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit
          };
          sendTelemetry(data, temperatureSchema)
        }, 5000);

        var sendHumidityInterval = setInterval(function () {
          humidity += generateRandomIncrement();
          var data = {
            'humidity': humidity,
            'humidity_unit': humidityUnit
          };
          sendTelemetry(data, humiditySchema)
        }, 5000);

        var sendPressureInterval = setInterval(function () {
          pressure += generateRandomIncrement();
          var data = {
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, pressureSchema)
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

1. Uložit změny **remote_monitoring.js** souboru.

1. Spuštění ukázkové aplikace, spusťte na příkazovém řádku následující příkaz:

    ```cmd/sh
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
