---
title: Začínáme se správou zařízení Azure IoT Hub (Node) | Microsoft Docs
description: Použití správy zařízení IoT Hub k zahájení restartování vzdáleného zařízení. Pomocí sady Azure IoT SDK pro Node.js implementovat aplikaci simulovaného zařízení, která zahrnuje přímou metodu a aplikaci služby, která volá přímou metodu.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: cfc0fa45c08f917b2e0b4a0b055e801173a4ba39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91251998"
---
# <a name="get-started-with-device-management-nodejs"></a>Začínáme se správou zařízení (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí [Azure Portal](https://portal.azure.com) můžete vytvořit IoT Hub a vytvořit identitu zařízení ve službě IoT Hub.

* Vytvořte aplikaci simulovaného zařízení, která obsahuje přímou metodu, která toto zařízení restartuje. Přímé metody jsou vyvolány z cloudu.

* Vytvořte konzolovou aplikaci Node.js, která v aplikaci simulovaného zařízení zavolá přímou metodu Restarter ve službě IoT Hub.

Na konci tohoto kurzu máte dvě Node.js konzolové aplikace:

* **dmpatterns_getstarted_device.js**, která se připojí ke službě IoT Hub s dříve vytvořenou identitou zařízení, obdrží přímo metodu restart, simuluje fyzické restartování a oznamuje čas posledního restartování.

* **dmpatterns_getstarted_service.js**, která volá přímou metodu v aplikaci simulovaného zařízení, zobrazuje odpověď a zobrazuje aktualizované hlášené vlastnosti.

## <a name="prerequisites"></a>Požadavky

* Node.js verze 10.0. x nebo novější. [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) popisuje, jak nainstalovat Node.js pro tento kurz v systému Windows nebo Linux.

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části:

* Vytvoříte konzolovou aplikaci Node.js, která bude reagovat na přímou metodu volanou cloudem.

* Aktivace simulovaného restartování zařízení

* Pomocí hlášených vlastností povolit nefungující dotazy zařízení k identifikaci zařízení a jejich poslední restartování

1. Vytvořte prázdnou složku s názvem **manageddevice**.  Ve složce **manageddevice** vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

2. Na příkazovém řádku ve složce **manageddevice** spusťte následující příkaz k instalaci balíčku sady SDK pro zařízení **Azure-IoT-Device** a balíčku **Azure-IoT-Device-MQTT** :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Pomocí textového editoru vytvořte soubor **dmpatterns_getstarted_device.js** ve složce **manageddevice** .

4. Přidejte následující příkazy ' vyžadovat ' na začátku **dmpatterns_getstarted_device.js** souboru:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Přidejte proměnnou **connectionString** a použijte ji k vytvoření instance **klienta**.  Nahraďte `{yourdeviceconnectionstring}` hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste zkopírovali dříve v [části registrace nového zařízení ve službě IoT Hub](#register-a-new-device-in-the-iot-hub).  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Přidejte následující funkci, která implementuje přímou metodu na zařízení.

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

7. Otevřete připojení ke službě IoT Hub a spusťte naslouchací proces přímé metody:

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

8. Uložte a zavřete soubor **dmpatterns_getstarted_device.js** .

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo v článku, [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivace vzdáleného restartování zařízení pomocí přímé metody

V této části vytvoříte konzolovou aplikaci Node.js, která inicializuje vzdálené restartování na zařízení pomocí přímé metody. Aplikace pomocí dvojitých dotazů na zařízení zjistí čas posledního restartování tohoto zařízení.

1. Vytvořte prázdnou složku s názvem **triggerrebootondevice**. Ve složce **triggerrebootondevice** vytvořte package.jsv souboru pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

2. Na příkazovém řádku ve složce **triggerrebootondevice** spusťte následující příkaz, který nainstaluje balíček sady SDK pro zařízení **Azure-iothub** a balíček **Azure-IoT-Device-MQTT** :

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Pomocí textového editoru vytvořte soubor **dmpatterns_getstarted_service.js** ve složce **triggerrebootondevice** .

4. Přidejte následující příkazy ' vyžadovat ' na začátku **dmpatterns_getstarted_service.js** souboru:

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Přidejte následující deklarace proměnných a nahraďte `{iothubconnectionstring}` hodnotu zástupného symbolu připojovacím řetězcem IoT Hub, který jste zkopírovali dříve v [části získání připojovacího řetězce centra IoT](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Přidejte následující funkci, která vyvolá metodu zařízení k restartování cílového zařízení:

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

7. Přidejte následující funkci, která se dotazuje na zařízení a získá čas posledního restartování:

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

8. Přidejte následující kód pro volání funkcí, které aktivují metodu restart Direct a dotaz na čas posledního restartování:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Uložte a zavřete soubor **dmpatterns_getstarted_service.js** .

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni spustit aplikace.

1. Na příkazovém řádku ve složce **manageddevice** spusťte následující příkaz, který zahájí naslouchání metodě restart přímo.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. Na příkazovém řádku ve složce **triggerrebootondevice** spusťte následující příkaz, který aktivuje vzdálené restartování a dotaz na vlákna zařízení, aby se našel čas posledního restartování.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. V konzole se zobrazí odpověď zařízení metoda přímé restartování a stav restartování.

   Následuje ukázka reakce zařízení na přímou metodu pro restartování, kterou odesílá služba:

   ![výstup aplikace manageddevice](./media/iot-hub-node-node-device-management-get-started/device.png)

   Následující příklad ukazuje službu, která spouští restartování a cyklické dotazování zařízení na dobu posledního restartování:

   ![výstup aplikace triggerrebootondevice](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
