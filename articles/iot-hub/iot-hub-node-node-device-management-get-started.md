---
title: Začínáme se správou zařízení Azure IoT Hub (Node) | Dokumentace Microsoftu
description: Jak zahajte restartování vzdálené zařízení pomocí správy zařízení služby IoT Hub. Implementace aplikace simulovaného zařízení, která obsahuje metodu s přímým přístupem a app service, která vyvolá přímou metodu pomocí sady Azure IoT SDK pro Node.js.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: juanpere
ms.openlocfilehash: 278765957156b56c3c476678b8db0efe5fb73c68
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118264"
---
# <a name="get-started-with-device-management-node"></a>Začínáme se správou zařízení (Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí webu Azure portal k vytvoření služby IoT Hub a vytvoření identity zařízení ve službě IoT hub.
* Vytvoření aplikace simulovaného zařízení, která obsahuje přímé metody, která toto zařízení restartuje. Přímé metody jsou vyvolány z cloudu.
* Vytvořte konzolovou aplikaci Node.js, která volá metodu restartování s přímým přístupem v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.

Na konci tohoto kurzu budete mít dvě konzolové aplikace Node.js:

**dmpatterns_getstarted_device.js**, propojuje službu IoT hub s identitou zařízení vytvořenou dříve, obdrží restartování přímé metody, simuluje fyzické restartování a ohlásí, čas poslední restartování.

**dmpatterns_getstarted_service.js**, která volá metodu s přímým přístupem v aplikaci simulovaného zařízení, zobrazí odpověď a zobrazí aktualizovaný ohlášené vlastnosti.

Pro absolvování tohoto kurzu potřebujete:

* Node.js verze 4.0.x nebo novější, <br/>  [Příprava vývojového prostředí] [ lnk-dev-setup] popisuje postup instalace Node.js pro účely tohoto kurzu ve Windows nebo Linuxu.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro službu IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části provedete

* Vytvoříte konzolovou aplikaci Node.js, která bude reagovat na přímou metodu volanou cloudem.
* Aktivovat restartování simulovaného zařízení
* Pomocí ohlášených vlastností umožníte dotazů na dvojčata zařízení identifikovat zařízení a kdy se naposledy restartování

1. Vytvořte prázdnou složku s názvem **manageddevice**.  Ve složce **manageddevice** vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **manageddevice** složky, spusťte následující příkaz k instalaci **azure-iot-device** balíčku sady SDK pro zařízení a **azure-iot-device-mqtt** balíček:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Pomocí textového editoru, vytvořte **dmpatterns_getstarted_device.js** soubor **manageddevice** složky.
4. Přidejte následující příkazy na začátku "vyžadovat" **dmpatterns_getstarted_device.js** souboru:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Přidejte proměnnou **connectionString** a použijte ji k vytvoření instance **klienta**.  Nahraďte připojovací řetězec připojovacím řetězcem zařízení.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Přidejte následující funkci, která implementuje přímé metody v zařízení
   
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
7. Otevřete připojení ke službě IoT hub a spustit naslouchací proces přímé metody:
   
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
8. Uložte a zavřete **dmpatterns_getstarted_device.js** souboru.

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například exponenciální regresí), jak je navrženo v článku [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivační událost vzdálené restartování zařízení pomocí přímé metody
V této části vytvoříte konzolovou aplikaci Node.js, který iniciuje vzdálené restartování zařízení pomocí přímé metody. Aplikace používá dotazů na dvojčata zařízení ke zjištění poslední čas restartování pro dané zařízení.

1. Vytvořte prázdnou složku s názvem **triggerrebootondevice**.  V **triggerrebootondevice** složce vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **triggerrebootondevice** složky, spusťte následující příkaz k instalaci **azure-iothub** balíčku sady SDK pro zařízení a **azure-iot-device-mqtt** balíček:
   
    ```
    npm install azure-iothub --save
    ```
3. Pomocí textového editoru, vytvořte **dmpatterns_getstarted_service.js** soubor **triggerrebootondevice** složky.
4. Přidejte následující příkazy na začátku "vyžadovat" **dmpatterns_getstarted_service.js** souboru:
   
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
6. Přidejte následující funkci, která vyvolat metodu zařízení restartovat cílové zařízení:
   
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
7. Přidejte následující funkci, která dotazovat na zařízení a získat poslední čas restartování:
   
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
8. Přidejte následující kód k volání funkce, které aktivují přímé metody restartovat počítač a dotaz na poslední čas restartování:
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. Uložte a zavřete **dmpatterns_getstarted_service.js** souboru.

## <a name="run-the-apps"></a>Spouštění aplikací
Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku v **manageddevice** složky, spuštěním následujícího příkazu zahajte naslouchání pro přímé metody restartování.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Na příkazovém řádku v **triggerrebootondevice** složky, spusťte následující příkaz spustí vzdálené restartování a dotazů pro dvojče zařízení najít poslední čas restartování.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. Zobrazí se zařízení odpověď na přímé metody v konzole.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
