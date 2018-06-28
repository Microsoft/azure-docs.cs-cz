---
title: Plánování úloh službou Azure IoT Hub (uzel) | Microsoft Docs
description: Popisuje, jak naplánovat úlohu služby Azure IoT Hub pro vyvolání přímé metody na několika zařízeních. SDK služby Azure IoT pro Node.js použijete k implementaci aplikace simulovaného zařízení a aplikační služby, který chcete spustit úlohu.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: juanpere
ms.openlocfilehash: 42deb210c55cd4a6c2aa2c7757ed87f8f706c58f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "34634103"
---
# <a name="schedule-and-broadcast-jobs-node"></a>Úlohy plán a všesměrového vysílání (uzel)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub je plně spravovaná služba, která umožňuje back-end aplikačním k vytvoření a sledování úloh, které naplánovat a aktualizovat miliony zařízení.  Úlohy lze použít pro následující akce:

* Aktualizace požadovaných vlastností
* Aktualizace značky
* Vyvolání metody přímé

Úloha koncepčně, zabalí jednu z těchto akcí a sleduje průběh provádění na skupiny zařízení, který je definován v dotazu twin zařízení.  Back-end aplikace můžete například použít úlohu pro vyvolání metody restartu na 10 000 zařízení, určeného zařízení twin dotazu a naplánovány na datum v budoucnosti.  Aplikace pak můžete sledovat průběh každé z těchto zařízení přijímat a provést metodu restartování.

Další informace o každém z těchto funkcí v těchto článcích:

* Dvojče zařízení a vlastností: [začít pracovat s dvojčata zařízení] [ lnk-get-started-twin] a [kurz: použití dvojici vlastností zařízení][lnk-twin-props]
* Přímé metody: [Příručka vývojáře pro službu IoT Hub - přímé metody] [ lnk-dev-methods] a [kurz: přímé metody][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření aplikace simulovaného zařízení Node.js, která má přímý metodu, která umožňuje **lockDoor**, který je možné volat v back-end řešení.
* Vytvořte konzolovou aplikaci softwaru Node.js, která volá **lockDoor** přímá metoda v aplikaci simulovaného zařízení pomocí úlohy a aktualizace požadované vlastnosti pomocí úlohy zařízení.

Na konci tohoto kurzu máte dvě aplikace Node.js:

**simDevice.js**, který připojuje ke službě IoT hub s identitou zařízení a přijímá **lockDoor** přímá metoda.

**scheduleJobService.js**, která volá metodu přímé v aplikaci simulovaného zařízení a aktualizuje dvojče zařízení požadovaných vlastností pomocí úlohy.

Pro absolvování tohoto kurzu potřebujete:

* Verze Node.js 4.0.x nebo novější, <br/>  [Příprava vývojového prostředí] [ lnk-dev-setup] popisuje postup instalace Node.js pro tento návod v systému Windows nebo Linux.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části vytvoříte konzolovou aplikaci softwaru Node.js, která reaguje na přímé metodu s názvem cloudem, který aktivuje simulované **lockDoor** metoda.

1. Vytvořit novou prázdnou složku s názvem **simDevice**.  V **simDevice** složky, vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **simDevice** složky, spusťte následující příkaz k instalaci **azure-iot-device** balíčku sady SDK zařízení a **azure-iot zařízení mqtt** balíčku:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Pomocí textového editoru, vytvořte novou **simDevice.js** v soubor **simDevice** složky.
4. Přidejte následující příkazy na začátku "vyžadovat" **simDevice.js** souboru:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Přidejte proměnnou **connectionString** a použijte ji k vytvoření instance **klienta**.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Přidejte následující funkci pro zpracování **lockDoor** metoda.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. Přidejte následující kód pro obslužnou rutinu pro registraci **lockDoor** metoda.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. Uložte a zavřete **simDevice.js** souboru.

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byte měli implementovat zásady opakování (například exponenciální opakování), jak je navrženo v článku [Řešení přechodných chyb][lnk-transient-faults] na webu MSDN.
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Plán úlohy pro volání přímá metoda a aktualizace vlastností dvojče zařízení
V této části vytvoříte konzolovou aplikaci softwaru Node.js, který iniciuje vzdálené **lockDoor** na zařízení pomocí jiné metody přímé a aktualizovat vlastnosti dvojče zařízení.

1. Vytvořit novou prázdnou složku s názvem **scheduleJobService**.  V **scheduleJobService** složky, vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **scheduleJobService** složky, spusťte následující příkaz k instalaci **azure-iothub** balíčku sady SDK zařízení a **azure-iot zařízení mqtt** balíček:
   
    ```
    npm install azure-iothub uuid --save
    ```
3. Pomocí textového editoru, vytvořte novou **scheduleJobService.js** v soubor **scheduleJobService** složky.
4. Přidejte následující příkazy na začátku "vyžadovat" **dmpatterns_gscheduleJobServiceetstarted_service.js** souboru:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```
5. Přidejte následující deklarace proměnných a nahraďte zástupné hodnoty:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
6. Přidejte následující funkce, které se používá k monitorování provádění úlohy:
   
    ```
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
7. Přidejte následující kód do naplánovat úlohu, která volá metodu zařízení:
   
    ```
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
8. Přidejte následující kód k naplánování úlohy aktualizace dvojče zařízení:
   
    ```
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
9. Uložte a zavřete **scheduleJobService.js** souboru.

## <a name="run-the-applications"></a>Spuštění aplikací
Nyní můžete spustit aplikace.

1. Na příkazovém řádku v **simDevice** složky, spusťte následující příkaz, aby začal přijímat přímá metoda restartování.
   
    ```
    node simDevice.js
    ```
2. Na příkazovém řádku v **scheduleJobService** složky, spusťte následující příkaz k aktivaci úlohy, které mají zamknout dveře a aktualizovat twin
   
    ```
    node scheduleJobService.js
    ```
3. Zobrazí zařízení odpověď na přímá metoda v konzole.

## <a name="next-steps"></a>Další postup
V tomto kurzu jste použili úlohu při plánování přímá metoda zařízení a aktualizaci vlastností dvojče zařízení.

Chcete-li pokračovat, Začínáme se službou IoT Hub a vzory správy zařízení, jako je vzdálené přes aktualizaci firmwaru letecké, najdete v části:

[Kurz: Jak to provést aktualizaci firmwaru][lnk-fwupdate]

Chcete-li pokračovat, Začínáme se službou IoT Hub, najdete v části [Začínáme se službou Azure IoT Edge][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
