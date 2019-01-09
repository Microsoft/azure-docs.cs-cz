---
title: Plánování úloh s Azure IoT Hub (Node) | Dokumentace Microsoftu
description: Jak naplánovat úlohu služby Azure IoT Hub k vyvolání přímé metody v různá zařízení. Implementace služby aplikace na spuštění úlohy a aplikace simulovaného zařízení pomocí sady Azure IoT SDK pro Node.js.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: juanpere
ms.openlocfilehash: a0614b5a1eadafe78537a4793d2dc0a866337487
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118876"
---
# <a name="schedule-and-broadcast-jobs-node"></a>Úlohy vysílání a plánování (Node)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub je plně spravovaná služba, která umožňuje back endové aplikace vytvořit a sledovat úlohy, které naplánovat a aktualizovat miliony zařízení.  Úlohy lze použít pro následující akce:

* Aktualizace požadovaných vlastností
* Aktualizace značky
* Vyvolání přímých metod

Úloha koncepčně, zabalí jednu z těchto akcí a sleduje průběh provádění sady zařízení, která je definovaná dotazem dvojčete zařízení.  Například back endové aplikace úlohu můžete použít k vyvolání metody restartu na 10 000 zařízení, vybraných podle dotazu dvojče zařízení a naplánované na budoucí dobu.  Tuto aplikaci pak můžete sledovat průběh každé z těchto zařízení přijímat a proveďte metodu restartování.

Další informace o každé z těchto funkcí v těchto článcích:

* Dvojče zařízení a vlastnosti: [Začínáme s dvojčaty zařízení] [ lnk-get-started-twin] a [kurzu: Jak používat vlastnosti dvojčat zařízení][lnk-twin-props]
* Přímé metody: [Příručka pro vývojáře IoT Hub - přímých metod] [ lnk-dev-methods] a [kurz: přímé metody][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření aplikace simulovaného zařízení Node.js, která má přímé metody, která umožňuje **lockDoor**, které je možné vyvolat v back-endu řešení.
* Vytvořte konzolovou aplikaci Node.js, která volá **lockDoor** přímé metody v aplikaci simulovaného zařízení pomocí úlohy a aktualizace požadované vlastnosti pomocí úlohy zařízení.

Na konci tohoto kurzu budete mít dvě aplikace Node.js:

**simDevice.js**, která se připojuje ke službě IoT hub s identitou zařízení a přijímá **lockDoor** přímá metoda.

**scheduleJobService.js**, která volá metodu s přímým přístupem v aplikaci simulovaného zařízení a aktualizace dvojčete zařízení požadované vlastnosti pomocí úlohy.

Pro absolvování tohoto kurzu potřebujete:

* Node.js verze 4.0.x nebo novější, <br/>  [Příprava vývojového prostředí] [ lnk-dev-setup] popisuje postup instalace Node.js pro účely tohoto kurzu ve Windows nebo Linuxu.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro službu IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části vytvoříte konzolovou aplikaci Node.js, která bude reagovat na přímou metodu volanou cloudem, který aktivuje Simulovaná **lockDoor** metody.

1. Vytvořte novou prázdnou složku s názvem **simDevice**.  V **simDevice** složce vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **simDevice** složky, spusťte následující příkaz k instalaci **azure-iot-device** balíčku sady SDK pro zařízení a **azure-iot-device-mqtt** balíčku:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Pomocí textového editoru vytvořte nový **simDevice.js** soubor **simDevice** složky.
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
6. Přidejte následující funkci pro zpracování **lockDoor** metody.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. Přidejte následující kód pro obslužnou rutinu pro registraci **lockDoor** metody.
   
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
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například exponenciální regresí), jak je navrženo v článku [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Plánování úloh pro volání přímé metody a aktualizují se vlastnosti dvojčete zařízení
V této části vytvoříte konzolovou aplikaci Node.js, který iniciuje vzdálené **lockDoor** na zařízení pomocí přímé metody a aktualizovat vlastnosti dvojčete zařízení.

1. Vytvořte novou prázdnou složku s názvem **scheduleJobService**.  V **scheduleJobService** složce vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **scheduleJobService** složky, spusťte následující příkaz k instalaci **azure-iothub** balíčku sady SDK pro zařízení a **azure-iot-device-mqtt** balíček:
   
    ```
    npm install azure-iothub uuid --save
    ```
3. Pomocí textového editoru vytvořte nový **scheduleJobService.js** soubor **scheduleJobService** složky.
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
6. Přidejte následující funkci, která se používá k monitorování provádění úlohy:
   
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
7. Přidejte následující kód k naplánování úlohy, která volá metodu zařízení:
   
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
8. Přidejte následující kód k naplánování úlohy se aktualizovat dvojče zařízení:
   
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

1. Na příkazovém řádku v **simDevice** složky, spuštěním následujícího příkazu zahajte naslouchání pro přímé metody restartování.
   
    ```
    node simDevice.js
    ```
2. Na příkazovém řádku v **scheduleJobService** složky, spusťte následující příkaz k aktivaci úlohy, které mají zamknout dveře a aktualizovat dvojčeti
   
    ```
    node scheduleJobService.js
    ```
3. Zobrazí se zařízení odpověď na přímé metody v konzole.

## <a name="next-steps"></a>Další postup
V tomto kurzu používají úlohy k plánování přímé metody, která zařízení a aktualizace vlastnosti dvojčete zařízení.

Chcete-li pokračovat v seznamování se službou IoT Hub a schémata správy zařízení jako vzdálené přes aktualizace firmwaru air, naleznete v tématu:

[Kurz: Jak provést upgrade firmwaru][lnk-fwupdate]

Chcete-li pokračovat v seznamování se službou IoT Hub, přečtěte si téma [Začínáme se službou Azure IoT Edge][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: tutorial-device-twins.md
[lnk-c2d-methods]: quickstart-control-device-node.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: tutorial-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
