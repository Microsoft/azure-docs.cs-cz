---
title: Zprávy typu cloud zařízení pomocí služby Azure IoT Hub (Node) | Dokumentace Microsoftu
description: Postup odesílání zpráv typu cloud zařízení na zařízení ze služby Azure IoT hub pomocí sad Azure IoT SDK pro Node.js. Upravíte aplikaci simulovaného zařízení pro příjem zpráv z cloudu do zařízení a upravovat back endové aplikace odesílat zprávy typu cloud zařízení.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: e2c3c3988193242cd0afe0135b019c7e6f73b59c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596721"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Odesílání zpráv typu cloud zařízení pomocí služby IoT Hub (Node)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Úvod
Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. [Začínáme se službou IoT Hub](quickstart-send-telemetry-node.md) kurz ukazuje postupy při vytvoření služby IoT hub, zřídit identitu zařízení v něm a kódu aplikace simulovaného zařízení, která odesílá zprávy typu zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz vychází [Začínáme se službou IoT Hub](quickstart-send-telemetry-node.md). To se dozvíte, jak do:

* Z back-end vašeho řešení odesílat zprávy typu cloud zařízení na jediné zařízení prostřednictvím služby IoT Hub.
* Příjem zpráv typu cloud zařízení na zařízení.
* Z back-end vašeho řešení, požádat o doručení potvrzení (*zpětnou vazbu*) pro zprávy odeslané do zařízení ze služby IoT Hub.

Můžete najít další informace o zprávy typu cloud zařízení v [Příručka vývojáře pro IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto kurzu spusťte dvě konzolové aplikace Node.js:

* **SimulatedDevice**, upravenou verzi aplikaci vytvořenou v [Začínáme se službou IoT Hub](quickstart-send-telemetry-node.md), který se připojí ke službě IoT hub a přijímá zprávy typu cloud zařízení.

* **SendCloudToDeviceMessage**, která odesílá zprávy typu cloud zařízení do aplikace simulovaného zařízení prostřednictvím služby IoT Hub a potom přijímá jeho doručení potvrzení.

> [!NOTE]
> IoT Hub má sady SDK podporují mnoho platforem zařízení a jazyky (včetně C, Javy a JavaScriptu) prostřednictvím sady SDK pro zařízení Azure IoT. Podrobné pokyny o tom, jak připojit zařízení ke kódu v tomto kurzu a obecně pro službu Azure IoT Hub, najdete v článku [centrum pro vývojáře Azure IoT](https://azure.microsoft.com/develop/iot).
>

Pro absolvování tohoto kurzu potřebujete:

* Verze Node.js 10.0.x nebo novější.
* Aktivní účet Azure. (Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial) během několika minut.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Příjem zpráv v aplikaci simulovaného zařízení

V této části upravíte aplikaci simulovaného zařízení, kterou jste vytvořili v [Začínáme se službou IoT Hub](quickstart-send-telemetry-node.md) pro příjem zpráv typu cloud zařízení ze služby IoT hub.

1. Pomocí textového editoru otevřete soubor SimulatedDevice.js.

2. Upravit **connectCallback** funkce pro zpracování zprávy odeslané ze služby IoT Hub. V tomto příkladu se zařízení vždy vyvolá **kompletní** funkce služby IoT Hub upozornit, že nezpracuje zprávy. Vaše nová verze **connectCallback** funkce bude vypadat jako následující fragment kódu:
   
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
   > Pokud pomocí protokolu HTTPS místo protokol MQTT nebo AMQP k přenosu **DeviceClient** instance kontroluje pro zprávy ze služby IoT Hub zřídka (méně než každých 25 minut). Další informace o rozdílech mezi podpora MQTT, AMQP a protokolu HTTPS a omezení šířky pásma služby IoT Hub, najdete v článku [Příručka vývojáře pro IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud zařízení

V této části vytvoříte konzolovou aplikaci Node.js, která odesílá zprávy typu cloud zařízení do aplikace simulovaného zařízení. ID zařízení, zařízení, které jste přidali v kroku budete potřebovat [Začínáme se službou IoT Hub](quickstart-send-telemetry-node.md) kurzu. Budete potřebovat připojovací řetězec služby IoT Hub pro vaše centrum, které můžete najít v [webu Azure portal](https://portal.azure.com).

1. Vytvořte prázdnou složku s názvem **sendcloudtodevicemessage**. V **sendcloudtodevicemessage** složce vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:

    ```shell
    npm init
    ```

2. Na příkazovém řádku v **sendcloudtodevicemessage** složky, spusťte následující příkaz k instalaci **azure-iothub** balíčku:
   
    ```shell
    npm install azure-iothub --save
    ```

3. Pomocí textového editoru, vytvořte **SendCloudToDeviceMessage.js** soubor **sendcloudtodevicemessage** složky.

4. Přidejte následující `require` příkazy na začátku **SendCloudToDeviceMessage.js** souboru:
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Přidejte následující kód, který **SendCloudToDeviceMessage.js** souboru. Nahraďte hodnotu zástupného symbolu "{připojovacího řetězce centra iot}" připojovacím řetězcem služby IoT Hub pro rozbočovač, kterou jste vytvořili v [Začínáme se službou IoT Hub](quickstart-send-telemetry-node.md) kurzu. Nahraďte zástupný text "{id zařízení}" s ID zařízení na zařízení, které jste přidali v kroku [Začínáme se službou IoT Hub](quickstart-send-telemetry-node.md) kurzu:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Přidejte následující funkci k tisku výsledků operace do konzoly:
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Přidejte následující funkci, která vytiskne doručování zpráv se zpětnou vazbou do konzoly:
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Přidejte následující kód k odeslání zprávy do zařízení a zpracovala zpráva zpětnou vazbu, když zařízení uznává zprávy typu cloud zařízení:
   
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

1. Na příkazovém řádku v **simulateddevice** složky, spusťte následující příkaz k odesílání telemetrických dat do služby IoT Hub a přijímat zprávy typu cloud zařízení:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![Spusťte aplikaci simulovaného zařízení](./media/iot-hub-node-node-c2d/receivec2d.png)

2. Na příkazovém řádku v **sendcloudtodevicemessage** složky, spusťte následující příkaz k odeslání zprávy typu cloud zařízení a počkat na zpětnou vazbu potvrzení:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Spuštění aplikace odesílat příkazy typu cloud zařízení](./media/iot-hub-node-node-c2d/sendc2d.png)
   
   > [!NOTE]
   > Pro saké pro zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například exponenciální regresí), jak je navrženo v článku [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak posílat a přijímat zprávy typu cloud zařízení. 

Příklady kompletní řešení začátku do konce, které používají služby IoT Hub najdete v tématu [akcelerátoru řešení vzdáleného monitorování Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Další informace o vývoji řešení s využitím služby IoT Hub, najdete v článku [Příručka vývojáře pro IoT Hub](iot-hub-devguide.md).