---
title: Plánování úloh pomocí Azure IoT Hub (Node) | Microsoft Docs
description: Jak naplánovat úlohu Azure IoT Hub k vyvolání přímé metody na více zařízeních. Sady SDK Azure IoT pro Node.js slouží k implementaci aplikací simulovaného zařízení a aplikace služby ke spuštění úlohy.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 2c5ce219e40117a2b656b7acab6dd68a1d20dffb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91251928"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Úlohy plánování a vysílání (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub je plně spravovaná služba, která umožňuje aplikacím back-end vytvářet a sledovat úlohy, které naplánují a aktualizují miliony zařízení.  Úlohy lze použít pro následující akce:

* Aktualizace požadovaných vlastností
* Aktualizovat značky
* Vyvolání přímých metod

V koncepčním případě úloha obaluje jednu z těchto akcí a sleduje průběh provádění na základě sady zařízení, která je definována dotazem se zdvojeným zařízením.  Například aplikace back-end může použít úlohu k vyvolání metody restart na zařízeních 10 000, která je určená dotazem typu v budoucnosti a naplánována v budoucím čase. Tato aplikace pak může sledovat průběh, protože každé zařízení obdrží a spustí metodu restartování.

Další informace o každé z těchto možností najdete v těchto článcích:

* Vlákna a vlastnosti zařízení: [Začínáme s dvojitými zprávami](iot-hub-node-node-twin-getstarted.md) a [kurzem zařízení: jak používat vlastnosti se zdvojeným zařízením](tutorial-device-twins.md)

* Přímé metody: [IoT Hub příručka pro vývojáře – přímé metody](iot-hub-devguide-direct-methods.md) a [kurz: přímé metody](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte Node.js aplikaci simulovaného zařízení, která má přímou metodu, která umožňuje **lockDoor**, kterou může volat back-end řešení.

* Vytvořte Node.js konzolovou aplikaci, která volá metodu **lockDoor** Direct v aplikaci simulovaného zařízení pomocí úlohy a aktualizuje požadované vlastnosti pomocí úlohy zařízení.

Na konci tohoto kurzu máte dvě Node.js aplikace:

* **simDevice.js**, která se připojí ke službě IoT Hub s identitou zařízení a přijímá přímo metodu **lockDoor** .

* **scheduleJobService.js**, která volá přímou metodu v aplikaci simulovaného zařízení a aktualizuje požadované vlastnosti v zařízení pomocí úlohy.

## <a name="prerequisites"></a>Požadavky

* Node.js verze 10.0. x nebo novější. [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) popisuje, jak nainstalovat Node.js pro tento kurz v systému Windows nebo Linux.

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT (neboli IoT Hubu)

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci Node.js, která reaguje na přímou metodu volanou cloudem, která aktivuje simulovanou metodu **lockDoor** .

1. Vytvořte novou prázdnou složku s názvem **simDevice**.  Ve složce **simDevice** vytvořte package.jsv souboru pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

   ```console
   npm init
   ```

2. Na příkazovém řádku ve složce **simDevice** spusťte následující příkaz k instalaci balíčku sady SDK pro zařízení **Azure-IoT-Device** a balíčku **Azure-IoT-Device-MQTT** :

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Pomocí textového editoru vytvořte nový soubor **simDevice.js** ve složce **simDevice** .

4. Přidejte následující příkazy ' vyžadovat ' na začátku **simDevice.js** souboru:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Přidejte proměnnou **connectionString** a použijte ji k vytvoření instance **klienta**. Nahraďte `{yourDeviceConnectionString}` hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste zkopírovali dříve.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Přidejte následující funkci pro zpracování metody **lockDoor** .

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

7. Přidejte následující kód pro registraci obslužné rutiny pro metodu **lockDoor** .

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

8. Uložte a zavřete soubor **simDevice.js** .

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo v článku, [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Plánování úloh pro volání přímé metody a aktualizace vlastností vlákna zařízení

V této části vytvoříte konzolovou aplikaci Node.js, která inicializuje vzdálenou **lockDoor** na zařízení pomocí přímé metody a aktualizuje vlastnosti vlákna zařízení.

1. Vytvořte novou prázdnou složku s názvem **scheduleJobService**.  Ve složce **scheduleJobService** vytvořte package.jsv souboru pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

    ```console
    npm init
    ```

2. Na příkazovém řádku ve složce **scheduleJobService** spusťte následující příkaz, který nainstaluje balíček sady SDK pro zařízení **Azure-iothub** a balíček **Azure-IoT-Device-MQTT** :

    ```console
    npm install azure-iothub uuid --save
    ```

3. Pomocí textového editoru vytvořte nový soubor **scheduleJobService.js** ve složce **scheduleJobService** .

4. Přidejte následující příkazy ' vyžadovat ' na začátku **scheduleJobService.js** souboru:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Přidejte následující deklarace proměnných. Nahraďte `{iothubconnectionstring}` hodnotu zástupného symbolu hodnotou, kterou jste zkopírovali v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string). Pokud jste zaregistrovali jiné zařízení než **myDeviceId**, nezapomeňte ho změnit v podmínce dotazu.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Přidejte následující funkci, která slouží k monitorování provádění úlohy:

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

7. Přidejte následující kód k naplánování úlohy, která volá metodu zařízení:
  
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

8. Přidejte následující kód k naplánování úlohy pro aktualizaci vlákna zařízení:

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

9. Uložte a zavřete soubor **scheduleJobService.js** .

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Na příkazovém řádku ve složce **simDevice** spusťte následující příkaz, který zahájí naslouchání metodě restart přímo.

    ```console
    node simDevice.js
    ```

2. Na příkazovém řádku ve složce **scheduleJobService** spusťte následující příkaz, který aktivuje úlohy pro uzamknutí dvířek a aktualizaci vlákna.

    ```console
    node scheduleJobService.js
    ```

3. V konzole se zobrazí odpověď zařízení k přímé metodě a stavu úlohy.

   Následující příklad ukazuje reakci zařízení přímo na metodu:

   ![Výstup aplikace simulovaného zařízení](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Níže vidíte úlohy plánování služby pro přímou metodu a aktualizaci s doplňováním zařízení a úlohy spuštěné k dokončení:

   ![Spuštění aplikace simulovaného zařízení](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili úlohu k naplánování přímé metody na zařízení a aktualizaci vlastností vlákna zařízení.

Pokud chcete pokračovat v seznámení se IoT Hub a způsoby správy zařízení, jako je například vzdálené prostřednictvím aktualizace firmwaru Air, přečtěte si téma [kurz: jak provést aktualizaci firmwaru](tutorial-firmware-update.md).

Pokud chcete pokračovat v seznámení s IoT Hub, přečtěte si téma [Začínáme s Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
