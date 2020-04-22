---
title: Plánování úloh pomocí služby Azure IoT Hub (uzel) | Dokumenty společnosti Microsoft
description: Jak naplánovat úlohu služby Azure IoT Hub k vyvolání přímé metody na více zařízeních. Sady Azure IoT SDK pro node.js slouží k implementaci aplikací simulovaných zařízení a aplikace služby ke spuštění úlohy.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt
ms.openlocfilehash: d7f9ce37ad85d39388eea90af263f59ce312a6b8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732272"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Úlohy plánování a vysílání (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub je plně spravovaná služba, která umožňuje back-endové aplikaci vytvářet a sledovat úlohy, které plánují a aktualizují miliony zařízení.  Úlohy lze použít pro následující akce:

* Aktualizace požadovaných vlastností
* Aktualizovat značky
* Vyvolat přímé metody

Koncepčně úloha zabalí jednu z těchto akcí a sleduje průběh provádění proti sadě zařízení, která je definována dotazem dvojčete zařízení.  Například back-endová aplikace může použít úlohu k vyvolání metody restartování na 10 000 zařízeních, určená dotazem dvojčete zařízení a naplánovaná v budoucnu. Tato aplikace pak můžete sledovat průběh jako každé z těchto zařízení přijímat a spouštět metodu restartování.

Další informace o jednotlivých funkcích najdete v těchto článcích:

* Dvojče zařízení a vlastnosti: [Začínáme s dvojčaty zařízení](iot-hub-node-node-twin-getstarted.md) a [kurz: Jak používat vlastnosti dvojčete zařízení](tutorial-device-twins.md)

* Přímé metody: [Průvodce vývojářem ioT Hubu - přímé metody](iot-hub-devguide-direct-methods.md) a [výuka: přímé metody](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte aplikaci se simulovaným zařízením Node.js, která má přímou metodu, která umožňuje **lockDoor**, který může být volán back-endem řešení.

* Vytvořte konzolovou aplikaci Node.js, která volá přímou metodu **lockDoor** v aplikaci simulovaného zařízení pomocí úlohy a aktualizuje požadované vlastnosti pomocí úlohy zařízení.

Na konci tohoto kurzu máte dvě aplikace Node.js:

* **simDevice.js**, který se připojuje k centru IoT s identitou zařízení a přijímá metodu **lockDoor** direct.

* **scheduleJobService.js**, který volá přímou metodu v aplikaci simulovaného zařízení a aktualizuje požadované vlastnosti dvojčete zařízení pomocí úlohy.

## <a name="prerequisites"></a>Požadavky

* Node.js verze 10.0.x nebo novější. [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) popisuje, jak nainstalovat soubor Node.js pro tento kurz v systému Windows nebo Linux.

* Aktivní účet Azure. (Pokud nemáte účet, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci Node.js, která reaguje na přímou metodu volanou cloudem, která aktivuje simulovanou metodu **lockDoor.**

1. Vytvořte novou prázdnou složku s názvem **simDevice**.  Ve složce **simDevice** vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

   ```console
   npm init
   ```

2. Na příkazovém řádku ve složce **simDevice** spusťte následující příkaz pro instalaci balíčku **Azure-iot-device** Device SDK a balíčku **azure-iot-device-mqtt:**

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Pomocí textového editoru vytvořte nový soubor **simDevice.js** ve složce **simDevice.**

4. Přidejte následující příkazy 'require' na začátku souboru **simDevice.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Přidejte proměnnou **connectionString** a použijte ji k vytvoření instance **klienta**. `{yourDeviceConnectionString}` Nahraďte zástupnou hodnotu připojovacím řetězcem zařízení, který jste zkopírovali dříve.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Přidejte následující funkci pro zpracování metody **lockDoor.**

    ```javascript
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

7. Přidejte následující kód pro registraci obslužné rutiny pro metodu **lockDoor.**

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Uložte a zavřete soubor **simDevice.js.**

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální backoff), jak je navrženo v článku [Přechodné zpracování chyb](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Naplánujte úlohy pro volání přímé metody a aktualizaci vlastností dvojčete zařízení

V této části vytvoříte konzolovou aplikaci Node.js, která iniciuje vzdálené **lockDoor** na zařízení pomocí přímé metody a aktualizuje vlastnosti dvojčete zařízení.

1. Vytvořte novou prázdnou složku nazvanou **scheduleJobService**.  Ve složce **scheduleJobService** vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

    ```console
    npm init
    ```

2. Na příkazovém řádku ve složce **scheduleJobService** spusťte následující příkaz pro instalaci balíčku **Azure-iothub** Device SDK a balíčku **azure-iot-device-mqtt:**

    ```console
    npm install azure-iothub uuid --save
    ```

3. Pomocí textového editoru vytvořte nový soubor **scheduleJobService.js** ve složce **scheduleJobService.**

4. Na začátek souboru **scheduleJobService.js** přidejte následující příkazy require:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Přidejte následující deklarace proměnných. Nahraďte `{iothubconnectionstring}` zástupnou hodnotu hodnotou, kterou jste zkopírovali v [připojovacím řetězci služby IoT hub](#get-the-iot-hub-connection-string). Pokud jste zaregistrovali zařízení jiné než **myDeviceId**, nezapomeňte jej změnit v podmínce dotazu.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Přidejte následující funkci, která slouží ke sledování provádění úlohy:

    ```javascript
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Přidejte následující kód pro naplánování úlohy, která volá metodu zařízení:
  
    ```javascript
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Přidejte následující kód pro naplánování úlohy pro aktualizaci dvojčete zařízení:

    ```javascript
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Uložte a zavřete soubor **scheduleJobService.js.**

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Na příkazovém řádku ve složce **simDevice** spusťte následující příkaz a začněte poslouchat metodu přímého restartování.

    ```console
    node simDevice.js
    ```

2. Na příkazovém řádku ve složce **scheduleJobService** spusťte následující příkaz, který spustí úlohy pro uzamčení dveří a aktualizaci dvojčete

    ```console
    node scheduleJobService.js
    ```

3. Zobrazí se odpověď zařízení na přímou metodu a stav úlohy v konzole.

   Následující ukazuje odpověď zařízení na přímou metodu:

   ![Výstup aplikace simulované zařízení](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Následující text ukazuje úlohy plánování služeb pro přímou metodu a aktualizaci dvojčete zařízení a úlohy spuštěné k dokončení:

   ![Spuštění aplikace simulované zařízení](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili úlohu k naplánování přímé metody do zařízení a aktualizace vlastností dvojčete zařízení.

Chcete-li pokračovat v začínáme s IoT Hub a vzory správy zařízení, jako je například vzdálené přes aktualizaci firmwaru vzduchu, naleznete [v tématu Návod: Jak to udělat aktualizaci firmwaru](tutorial-firmware-update.md).

Další informace o tom, jak začít s IoT Hubem, najdete [v tématu Začínáme s Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
