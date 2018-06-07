---
title: Začínáme se správou zařízení Azure IoT Hub (uzel) | Microsoft Docs
description: Jak používat k zahájení restartu zařízení vzdálenou správou zařízení IoT Hub. K implementaci aplikace simulovaného zařízení, která zahrnuje přímá metoda a aplikační služby, která volá metodu přímé používáte Azure IoT SDK pro Node.js.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: juanpere
ms.openlocfilehash: 54658ea72ac8e32db45774e87e3ab177d68046fa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635946"
---
# <a name="get-started-with-device-management-node"></a>Začínáme se správou zařízení (uzel)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Použití portálu Azure k vytvoření služby IoT Hub a vytvoření identity zařízení ve službě IoT hub.
* Vytvoření aplikace simulovaného zařízení, která obsahuje přímý metodu, která restartování zařízení. Přímé metody jsou vyvolány z cloudu.
* Vytvořte konzolovou aplikaci softwaru Node.js, která volá metodu restartování přímé v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.

Na konci tohoto kurzu máte dvě aplikace konzoly Node.js:

**dmpatterns_getstarted_device.js**, který připojí ke službě IoT hub s identitou zařízení vytvořenou dříve, obdrží přímá metoda restartování, simuluje restartu fyzické a sestavy čas posledního restartování.

**dmpatterns_getstarted_service.js**, která volá metodu přímé v aplikaci simulovaného zařízení, zobrazí odpověď a zobrazí aktualizovaná hlášené vlastnosti.

Pro absolvování tohoto kurzu potřebujete:

* Verze Node.js 4.0.x nebo novější, <br/>  [Příprava vývojového prostředí] [ lnk-dev-setup] popisuje postup instalace Node.js pro tento návod v systému Windows nebo Linux.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části

* Vytvoříte konzolovou aplikaci Node.js, která bude reagovat na přímou metodu volanou cloudem.
* Aktivační události restartu simulovaného zařízení
* Pomocí hlášen vlastnostech povolit dotazy na twin zařízení k identifikaci zařízení, a když trvají restartovat

1. Vytvořte prázdnou složku s názvem **manageddevice**.  Ve složce **manageddevice** vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **manageddevice** složky, spusťte následující příkaz k instalaci **azure-iot-device** balíčku sady SDK zařízení a **azure-iot zařízení mqtt** balíčku:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Pomocí textového editoru, vytvořte **dmpatterns_getstarted_device.js** v soubor **manageddevice** složky.
4. Přidejte následující příkazy na začátku "vyžadovat" **dmpatterns_getstarted_device.js** souboru:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Přidejte proměnnou **connectionString** a použijte ji k vytvoření instance **klienta**.  Nahraďte připojovacího řetězce připojovacím řetězcem zařízení.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Přidejte následující funkci implementovat metodu přímé na zařízení
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
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
7. Otevření připojení do služby IoT hub a spuštění nástroje pro sledování přímá metoda:
   
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
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byte měli implementovat zásady opakování (například exponenciální opakování), jak je navrženo v článku [Řešení přechodných chyb][lnk-transient-faults] na webu MSDN.

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivační události restartu vzdálené v zařízení s přímá metoda
V této části vytvoříte konzolovou aplikaci softwaru Node.js, který iniciuje vzdálené restartování na zařízení pomocí přímého metody. Aplikace používá dotazy twin zařízení Pokud chcete zjistit, čas posledního restartování pro toto zařízení.

1. Vytvořit prázdnou složku s názvem **triggerrebootondevice**.  V **triggerrebootondevice** složky, vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **triggerrebootondevice** složky, spusťte následující příkaz k instalaci **azure-iothub** balíčku sady SDK zařízení a **azure-iot zařízení mqtt** balíčku:
   
    ```
    npm install azure-iothub --save
    ```
3. Pomocí textového editoru, vytvořte **dmpatterns_getstarted_service.js** v soubor **triggerrebootondevice** složky.
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
6. Přidejte následující funkci k vyvolání metody zařízení restartování cílového zařízení:
   
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
7. Přidejte následující funkci k dotazování pro zařízení a získat čas posledního restartování:
   
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
8. Přidejte následující kód k volání funkce, které aktivovat přímá metoda restartování a dotazů pro poslední restartování:
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. Uložte a zavřete **dmpatterns_getstarted_service.js** souboru.

## <a name="run-the-apps"></a>Spouštění aplikací
Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku v **manageddevice** složky, spusťte následující příkaz, aby začal přijímat přímá metoda restartování.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Na příkazovém řádku v **triggerrebootondevice** složky, spusťte následující příkaz k aktivační události restartovat vzdálený počítač a dotaz pro dvojče zařízení najít poslední čas restartování počítače.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. Zobrazí zařízení odpověď na přímá metoda v konzole.

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
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
