---
title: Začínáme se správou zařízení Azure IoT Hub (Node) | Dokumenty společnosti Microsoft
description: Jak pomocí správy zařízení služby IoT Hub zahájit restartování vzdáleného zařízení. Pomocí sady Azure IoT SDK pro Node.js implementovat simulované zařízení aplikace, která obsahuje přímou metodu a aplikace služby, která vyvolá přímou metodu.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 5b2e4c03347020b5d5fc67927165403f06854e0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110914"
---
# <a name="get-started-with-device-management-nodejs"></a>Začínáme se správou zařízení (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí [portálu Azure](https://portal.azure.com) vytvořte službu IoT Hub a vytvořte identitu zařízení ve svém centru IoT hub.

* Vytvořte simulovanou aplikaci zařízení, která obsahuje přímou metodu, která restartuje toto zařízení. Přímé metody jsou vyvolány z cloudu.

* Vytvořte konzolovou aplikaci Node.js, která volá metodu přímého restartování v aplikaci simulovaných zařízení prostřednictvím služby IoT hub.

Na konci tohoto kurzu máte dvě konzolové aplikace Node.js:

* **dmpatterns_getstarted_device.js**, který se připojuje k centru IoT s identitou zařízení vytvořenou dříve, obdrží metodu přímého restartování, simuluje fyzické restartování a hlásí čas posledního restartování.

* **dmpatterns_getstarted_service.js**, který volá přímou metodu v aplikaci simulované zařízení, zobrazí odpověď a zobrazí aktualizované hlášené vlastnosti.

## <a name="prerequisites"></a>Požadavky

* Node.js verze 10.0.x nebo novější. [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) popisuje, jak nainstalovat soubor Node.js pro tento kurz v systému Windows nebo Linux.

* Aktivní účet Azure. (Pokud nemáte účet, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části:

* Vytvoříte konzolovou aplikaci Node.js, která bude reagovat na přímou metodu volanou cloudem.

* Spuštění simulovaného restartování zařízení

* Pomocí ohlášených vlastností povolte dotazy na dvojče zařízení k identifikaci zařízení a při jejich posledním restartování

1. Vytvořte prázdnou složku s názvem **manageddevice**.  Ve složce **manageddevice** vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

2. Na příkazovém řádku ve složce **manageddevice** spusťte následující příkaz pro instalaci balíčku **Azure-iot-device** Device SDK a balíčku **azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Pomocí textového editoru vytvořte soubor **dmpatterns_getstarted_device.js** ve složce **manageddevice.**

4. Na začátek souboru **dmpatterns_getstarted_device.js** přidejte následující příkazy require:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Přidejte proměnnou **connectionString** a použijte ji k vytvoření instance **klienta**.  `{yourdeviceconnectionstring}` Nahraďte zástupnou hodnotu připojovacím řetězcem zařízení, který jste dříve zkopírovali v [části Registrovat nové zařízení v centru IoT](#register-a-new-device-in-the-iot-hub).  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Přidejte následující funkci pro implementaci přímé metody na zařízení

    ```javascript
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

7. Otevřete připojení k centru IoT hub a spusťte naslouchací proces přímé metody:

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Uložte a zavřete soubor **dmpatterns_getstarted_device.js.**

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální backoff), jak je navrženo v článku [Přechodné zpracování chyb](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Spuštění vzdáleného restartování zařízení pomocí přímé metody

V této části vytvoříte konzolovou aplikaci Node.js, která iniciuje vzdálené restartování zařízení pomocí přímé metody. Aplikace používá dotazy na dvojče zařízení ke zjištění času posledního restartování pro toto zařízení.

1. Vytvořte prázdnou složku s názvem **triggerrebootondevice**. Ve složce **triggerrebootondevice** vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

2. Na příkazovém řádku ve složce **triggerrebootondevice** spusťte následující příkaz pro instalaci balíčku **Azure-iothub** Device SDK a balíčku **azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Pomocí textového editoru vytvořte soubor **dmpatterns_getstarted_service.js** ve složce **triggerrebootondevice.**

4. Na začátek souboru **dmpatterns_getstarted_service.js** přidejte následující příkazy require:

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Přidejte následující deklarace proměnných a nahraďte `{iothubconnectionstring}` zástupnou hodnotu připojovacím řetězcem služby IoT hub, který jste dříve zkopírovali v [připojovacím řetězci Služby IoT hub](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Přidejte následující funkci pro vyvolání metody zařízení pro restartování cílového zařízení:

    ```javascript
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

7. Přidejte následující funkci pro dotaz na zařízení a získejte čas posledního restartování:

    ```javascript
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

8. Přidejte následující kód pro volání funkcí, které aktivují metodu přímého restartování a dotaz na čas posledního restartování:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Uložte a zavřete soubor **dmpatterns_getstarted_service.js.**

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni ke spuštění aplikací.

1. Na příkazovém řádku ve složce **manageddevice** spusťte následující příkaz a začněte naslouchat metodě přímého restartování.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. Na příkazovém řádku ve složce **triggerrebootondevice** spusťte následující příkaz a spusťte vzdálené restartování a dotaz na dvojče zařízení, abyste našli čas posledního restartování.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Zobrazí se odpověď zařízení na metodu přímého restartování a stav restartování v konzoli.

   Následující ukazuje odpověď zařízení na metodu přímého restartování odeslanou službou:

   ![výstup aplikace spravovaného zařízení](./media/iot-hub-node-node-device-management-get-started/device.png)

   Následující ukazuje službu, která spouští restartování a dotazování dvojčete zařízení pro poslední čas restartování:

   ![výstup aplikace triggerrebootondevice](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
