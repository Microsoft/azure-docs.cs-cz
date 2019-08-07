---
title: Začínáme se správou zařízení Azure IoT Hub (Node) | Microsoft Docs
description: Použití správy zařízení IoT Hub k zahájení restartování vzdáleného zařízení. Pomocí sady Azure IoT SDK pro Node. js implementujete aplikaci simulovaného zařízení, která obsahuje přímou metodu a aplikaci služby, která volá přímou metodu.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.openlocfilehash: 88d9abda7d56deefc5880eb50799ac89a89ac44f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780956"
---
# <a name="get-started-with-device-management-nodejs"></a>Začínáme se správou zařízení (Node. js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí [Azure Portal](https://portal.azure.com) můžete vytvořit IoT Hub a vytvořit identitu zařízení ve službě IoT Hub.

* Vytvořte aplikaci simulovaného zařízení, která obsahuje přímou metodu, která toto zařízení restartuje. Přímé metody jsou vyvolány z cloudu.

* Vytvořte konzolovou aplikaci Node. js, která v aplikaci simulovaného zařízení zavolá přímou metodu restartu ve službě IoT Hub.

Na konci tohoto kurzu máte dvě konzolové aplikace Node. js:

* **dmpatterns_getstarted_device. js**, který se připojí ke službě IoT Hub s dříve vytvořenou identitou zařízení, obdrží přímou metodu restart, simuluje fyzické restartování a oznamuje čas posledního restartování.

* **dmpatterns_getstarted_service. js**, který volá přímou metodu v aplikaci simulovaného zařízení, zobrazuje odpověď a zobrazuje aktualizované hlášené vlastnosti.

Pro absolvování tohoto kurzu potřebujete:

* Node. js verze 10.0. x nebo novější. [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) popisuje, jak nainstalovat Node. js pro tento kurz v systému Windows nebo Linux.

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části provedete následující kroky:

* Vytvoříte konzolovou aplikaci Node.js, která bude reagovat na přímou metodu volanou cloudem.

* Aktivace simulovaného restartování zařízení

* Pomocí hlášených vlastností povolit nefungující dotazy zařízení k identifikaci zařízení a jejich poslední restartování

1. Vytvořte prázdnou složku s názvem **manageddevice**.  Ve složce **manageddevice** vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
      
    ```
    npm init
    ```

2. Na příkazovém řádku ve složce **manageddevice** spusťte následující příkaz k instalaci balíčku sady SDK pro zařízení **Azure-IoT-Device** a balíčku **Azure-IoT-Device-MQTT** :
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Pomocí textového editoru vytvořte ve složce **manageddevice** soubor **dmpatterns_getstarted_device. js** .

4. Na začátek souboru **dmpatterns_getstarted_device. js** přidejte následující příkazy ' vyžadovat ':
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Přidejte proměnnou **connectionString** a použijte ji k vytvoření instance **klienta**.  Nahraďte připojovací řetězec pomocí připojovacího řetězce zařízení.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Přidejte následující funkci, která implementuje přímou metodu na zařízení.
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Otevřete připojení ke službě IoT Hub a spusťte naslouchací proces přímé metody:

   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Uložte a zavřete soubor **dmpatterns_getstarted_device. js** .

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo v článku, [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivace vzdáleného restartování zařízení pomocí přímé metody

V této části vytvoříte konzolovou aplikaci Node. js, která inicializuje vzdálené restartování na zařízení pomocí přímé metody. Aplikace pomocí dvojitých dotazů na zařízení zjistí čas posledního restartování tohoto zařízení.

1. Vytvořte prázdnou složku s názvem **triggerrebootondevice**. Ve složce **triggerrebootondevice** vytvořte soubor Package. JSON pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```

2. Na příkazovém řádku ve složce **triggerrebootondevice** spusťte následující příkaz, který nainstaluje balíček sady SDK pro zařízení **Azure-iothub** a balíček **Azure-IoT-Device-MQTT** :
   
    ```
    npm install azure-iothub --save
    ```

3. Pomocí textového editoru vytvořte ve složce **triggerrebootondevice** soubor **dmpatterns_getstarted_service. js** .

4. Na začátek souboru **dmpatterns_getstarted_service. js** přidejte následující příkazy ' vyžadovat ':

  
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Přidejte následující deklarace proměnných a nahraďte zástupné hodnoty:

   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Přidejte následující funkci, která vyvolá metodu zařízení k restartování cílového zařízení:
   
    ```
    var startRebootDevice = function(twin) {
   
        var methodName = "reboot";
   
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
   
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Přidejte následující funkci, která se dotazuje na zařízení a získá čas posledního restartování:
   
    ```
    var queryTwinLastReboot = function() {
   
        registry.getTwin(deviceToReboot, function(err, twin){
   
            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Přidejte následující kód pro volání funkcí, které aktivují metodu restart Direct a dotaz na čas posledního restartování:

   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Uložte a zavřete soubor **dmpatterns_getstarted_service. js** .

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku ve složce **manageddevice** spusťte následující příkaz, který zahájí naslouchání metodě restart přímo.

   
    ```
    node dmpatterns_getstarted_device.js
    ```

2. Na příkazovém řádku ve složce **triggerrebootondevice** spusťte následující příkaz, který aktivuje vzdálené restartování a dotaz na vlákna zařízení, aby se našel čas posledního restartování.

   
    ```
    node dmpatterns_getstarted_service.js
    ```

3. V konzole se zobrazí odpověď zařízení k přímé metodě.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]