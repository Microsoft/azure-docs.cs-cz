---
title: Připojení zařízení pomocí Node.js | Dokumentace Microsoftu
description: Popisuje, jak připojit zařízení k Azure IoT Suite předkonfigurované řešení vzdáleného monitorování pomocí aplikace napsané v Node.js.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 87a2e97638508eef1d90a219cfb38d1fcac81d55
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094489"
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Připojení zařízení předkonfigurovaného řešení vzdáleného monitorování (Node.js)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-nodejs-sample-solution"></a>Vytvoření ukázkové řešení node.js

Zajistit, že verze Node.js 0.11.5 nebo novější nainstalován na vývojovém počítači. Můžete spustit `node --version` příkazového řádku, pokud chcete zkontrolovat verzi.

1. Vytvořte složku s názvem **RemoteMonitoring** na vývojovém počítači. Přejděte do této složky ve vašem prostředí příkazového řádku.

1. Spusťte následující příkazy ke stažení a instalaci balíčků, že které potřebujete k dokončení ukázkové aplikace:

    ```
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. V **RemoteMonitoring** složce vytvořte soubor s názvem **remote_monitoring.js**. Otevřete tento soubor v textovém editoru.

1. V **remote_monitoring.js** soubor, přidejte následující `require` příkazy:

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. Přidejte následující deklarace proměnných za příkazy `require`. Nahraďte zástupné hodnoty [Device Id] a [Device Key] hodnotami, které jste si pro své zařízení poznamenali na řídicím panelu řešení vzdáleného monitorování. K nahrazení hodnoty [IoTHub Name] použijte název hostitele služby IoT Hub z řídicího panelu řešení. Pokud je například název hostitele vaší služby IoT Hub **contoso.azure-devices.net**, nahraďte hodnotu [IoTHub Name] za **contoso**:

    ```nodejs
    var connectionString = 'HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Přidejte následující proměnné k definování některé základní telemetrická data:

    ```nodejs
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    ```

1. Přidejte následující pomocnou funkci k tisku výsledků operace:

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Přidejte následující pomocnou funkci používat k náhodné hodnoty telemetrie:

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Přidejte následující definice **DeviceInfo** objektu zařízení odesílá při spuštění:

    ```nodejs
    var deviceMetaData = {
        'ObjectType': 'DeviceInfo',
        'IsSimulatedDevice': 0,
        'Version': '1.0',
        'DeviceProperties': {
            'DeviceID': deviceId,
            'HubEnabledState': 1
        }
    };
    ```

1. Přidejte následující definice dvojčat zařízení hlásí hodnoty. Tato definice obsahuje popisy přímé metody, které zařízení podporuje:

    ```nodejs
    var reportedProperties = {
        "Device": {
            "DeviceState": "normal",
            "Location": {
                "Latitude": 47.642877,
                "Longitude": -122.125497
            }
        },
        "Config": {
            "TemperatureMeanValue": 56.7,
            "TelemetryInterval": 45
        },
        "System": {
            "Manufacturer": "Contoso Inc.",
            "FirmwareVersion": "2.22",
            "InstalledRAM": "8 MB",
            "ModelNumber": "DB-14",
            "Platform": "Plat 9.75",
            "Processor": "i3-9",
            "SerialNumber": "SER99"
        },
        "Location": {
            "Latitude": 47.642877,
            "Longitude": -122.125497
        },
        "SupportedMethods": {
            "Reboot": "Reboot the device",
            "InitiateFirmwareUpdate--FwPackageURI-string": "Updates device Firmware. Use parameter FwPackageURI to specifiy the URI of the firmware file"
        },
    }
    ```

1. Přidejte následující funkci pro zpracování **restartování** přímá volání metody:

    ```nodejs
    function onReboot(request, response) {
        // Implement actual logic here.
        console.log('Simulated reboot...');

        // Complete the response
        response.send(200, "Rebooting device", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

1. Přidejte následující funkci pro zpracování **InitiateFirmwareUpdate** přímá volání metody. Toto přímé metody používá parametr k určení umístění image firmwaru ke stažení a iniciuje firmwaru v zařízení asynchronní aktualizace:

    ```nodejs
    function onInitiateFirmwareUpdate(request, response) {
        console.log('Simulated firmware update initiated, using: ' + request.payload.FwPackageURI);

        // Complete the response
        response.send(200, "Firmware update initiated", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });

        // Add logic here to perform the firmware update asynchronously
    }
    ```

1. Přidejte následující kód k vytvoření instance klienta:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Přidejte následující kód do:

    * Otevření připojení.
    * Odeslat **DeviceInfo** objektu.
    * Nastavte obslužnou rutinu pro požadované vlastnosti.
    * Odešlete ohlášené vlastnosti.
    * Zaregistrujte obslužné rutiny pro přímé metody.
    * Začalo odesílat telemetrii.

    ```nodejs
    client.open(function (err) {
        if (err) {
            printErrorFor('open')(err);
        } else {
            console.log('Sending device metadata:\n' + JSON.stringify(deviceMetaData));
            client.sendEvent(new Message(JSON.stringify(deviceMetaData)), printErrorFor('send metadata'));

            // Create device twin
            client.getTwin(function(err, twin) {
                if (err) {
                    console.error('Could not get device twin');
                } else {
                    console.log('Device twin created');

                    twin.on('properties.desired', function(delta) {
                        console.log('Received new desired properties:');
                        console.log(JSON.stringify(delta));
                    });

                    // Send reported properties
                    twin.properties.reported.update(reportedProperties, function(err) {
                        if (err) throw err;
                        console.log('twin state reported');
                    });

                    // Register handlers for direct methods
                    client.onDeviceMethod('Reboot', onReboot);
                    client.onDeviceMethod('InitiateFirmwareUpdate', onInitiateFirmwareUpdate);
                }
            });

            // Start sending telemetry
            var sendInterval = setInterval(function () {
                temperature += generateRandomIncrement();
                externalTemperature += generateRandomIncrement();
                humidity += generateRandomIncrement();

                var data = JSON.stringify({
                    'DeviceID': deviceId,
                    'Temperature': temperature,
                    'Humidity': humidity,
                    'ExternalTemperature': externalTemperature
                });

                console.log('Sending device event data:\n' + data);
                client.sendEvent(new Message(data), printErrorFor('send event'));
            }, 5000);

            client.on('error', function (err) {
                printErrorFor('client')(err);
                if (sendInterval) clearInterval(sendInterval);
                client.close(printErrorFor('client.close'));
            });
        }
    });
    ```

1. Uložit změny **remote_monitoring.js** souboru.

1. Spusťte následující příkaz na příkazovém řádku spusťte ukázkovou aplikaci:
   
    ```
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
