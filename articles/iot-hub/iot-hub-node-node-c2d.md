---
title: Zprávy typu cloud zařízení s Azure IoT Hub (uzel) | Microsoft Docs
description: Postupy pro odesílání zpráv typu cloud zařízení pro zařízení ze služby Azure IoT hub pomocí sady Azure IoT SDK pro Node.js. Můžete upravit aplikaci simulovaného zařízení příjem zpráv typu cloud zařízení a úpravě back-end aplikace k odesílání zpráv typu cloud zařízení.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 640a7740e3451a8bd07fdea6a82af2f5cae4c25f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637459"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Odesílání zpráv typu cloud zařízení službou IoT Hub (uzel)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Úvod
Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-end řešení. [Začínáme s centrem IoT] kurz ukazuje, jak k vytvoření služby IoT hub, zřídit identitu zařízení v ní a kódu aplikaci ze simulovaného zařízení, která odesílá zprávy typu zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu vychází [Začínáme s centrem IoT]. Jak ukazuje na:

* Z back-end vašeho řešení odesílání zpráv typu cloud zařízení na jedno zařízení prostřednictvím služby IoT Hub.
* Příjem zpráv typu cloud zařízení na zařízení.
* Z back-end vašeho řešení, žádosti o potvrzení o doručení (*zpětné vazby*) pro zprávy odeslané do zařízení ze služby IoT Hub.

Můžete najít další informace o zprávy typu cloud zařízení v [Příručka vývojáře pro službu IoT Hub][IoT Hub developer guide - C2D].

Na konci tohoto kurzu můžete spustit dvě aplikace konzoly Node.js:

* **SimulatedDevice**, upravenou verzi aplikace vytvořená v [Začínáme s centrem IoT], který se připojuje ke službě IoT hub a přijímá zprávy typu cloud zařízení.
* **SendCloudToDeviceMessage**, který odešle zprávu cloud zařízení na aplikaci simulovaného zařízení prostřednictvím služby IoT Hub a pak obdrží jeho potvrzení o doručení.

> [!NOTE]
> IoT Hub je podpora v sadě SDK pro mnoho zařízení platformy a jazyky (například C, Javy a JavaScriptu) prostřednictvím SDK pro zařízení Azure IoT. Podrobné pokyny o tom, jak připojit zařízení ke kódu v tomto kurzu a obecně do služby Azure IoT Hub, najdete v článku [Centrum pro vývojáře Azure IoT].
> 
> 

Pro absolvování tohoto kurzu potřebujete:

* Node.js verze 4.0.x nebo novější.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

## <a name="receive-messages-in-the-simulated-device-app"></a>Příjem zpráv v aplikaci simulovaného zařízení
V této části upravíte aplikaci simulovaného zařízení, kterou jste vytvořili v [Začínáme s centrem IoT] pro příjem zpráv typu cloud zařízení ze služby IoT hub.

1. Pomocí textového editoru otevřete soubor SimulatedDevice.js.
2. Změnit **connectCallback** funkce pro zpracování zprávy odeslané ze služby IoT Hub. V tomto příkladu zařízení vždy vyvolá **dokončení** funkce IoT Hub oznámit, že zpracovala zprávy. Vaše nové verze **connectCallback** funkce vypadá jako následující fragment kódu:
   
    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);            
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
   > [!NOTE]
   > Pokud použijete protokol HTTPS místo MQTT nebo AMQP jako přenos, **DeviceClient** instance vyhledává zprávy ze služby IoT Hub zřídka (méně než každých 25 minut). Další informace o rozdílech mezi podpora MQTT, AMQP a HTTPS a omezení služby IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud zařízení
V této části vytvoříte konzolovou aplikaci softwaru Node.js, která posílání zpráv typu cloud zařízení do aplikaci simulovaného zařízení. Je třeba ID zařízení v zařízení, které jste přidali v [Začínáme s centrem IoT] kurzu. Připojovací řetězec služby IoT Hub pro vaše centrum, které můžete najít v musíte taky [Azure Portal].

1. Vytvořit prázdnou složku s názvem **sendcloudtodevicemessage**. V **sendcloudtodevicemessage** složky, vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:
   
    ```shell
    npm init
    ```
2. Na příkazovém řádku v **sendcloudtodevicemessage** složky, spusťte následující příkaz k instalaci **azure-iothub** balíčku:
   
    ```shell
    npm install azure-iothub --save
    ```
3. Pomocí textového editoru, vytvořte **SendCloudToDeviceMessage.js** v soubor **sendcloudtodevicemessage** složky.
4. Přidejte následující `require` příkazy na začátku **SendCloudToDeviceMessage.js** souboru:
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Přidejte následující kód, který **SendCloudToDeviceMessage.js** souboru. Nahraďte hodnotu zástupného symbolu "{iot hub připojovací řetězec}" připojovací řetězec služby IoT Hub pro rozbočovače, který jste vytvořili v [Začínáme s centrem IoT] kurzu. Nahraďte zástupný symbol "{zařízení id} s ID zařízení v zařízení, které jste přidali v [Začínáme s centrem IoT] kurzu:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Přidejte následující funkci k tisku výsledky operace do konzoly:
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Přidejte následující funkci k tisku doručení zpětnou vazbu zpráv do konzoly:
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Přidejte následující kód k odeslání zprávy do zařízení a zpracování zpráv zpětnou vazbu, je-li zařízení uznává zpráv typu cloud zařízení:
   
    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```
9. Uložte a zavřete **SendCloudToDeviceMessage.js** souboru.

## <a name="run-the-applications"></a>Spuštění aplikací
Nyní můžete spustit aplikace.

1. Na příkazovém řádku v **simulateddevice** složky, spusťte následující příkaz k odeslání telemetrie do služby IoT Hub a přijímat zprávy typu cloud zařízení:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![Spusťte aplikaci simulovaného zařízení][img-simulated-device]
2. Na příkazovém řádku v **sendcloudtodevicemessage** složky, spusťte následující příkaz k odeslání zprávy typu cloud zařízení a počkat na zpětnou vazbu potvrzení:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Spusťte aplikaci odesílat příkaz cloud zařízení][img-send-command]
   
   > [!NOTE]
   > Pro saké na jednoduchost tento kurz neimplementuje žádné zásady opakování. V produkčním kódu, měli byste implementovat zásady opakování (například exponenciálního omezení rychlosti), dle pokynů v článku na webu MSDN [Přechodná chyba zpracování].
   > 
   > 

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak odesílat a přijímat zprávy typu cloud zařízení. 

Příklady dokončení začátku do konce řešení, které pomocí služby IoT Hub, najdete v sekci [Accelerator řešení Azure IoT vzdálené monitorování].

Další informace o vývoji řešení službou IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Začínáme s centrem IoT]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Příručka vývojáře pro službu IoT Hub]: iot-hub-devguide.md
[Centrum pro vývojáře Azure IoT]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Přechodná chyba zpracování]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://portal.azure.com
[Accelerator řešení Azure IoT vzdálené monitorování]: https://azure.microsoft.com/documentation/suites/iot-suite/
