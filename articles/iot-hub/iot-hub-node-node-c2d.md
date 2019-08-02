---
title: Zprávy z cloudu na zařízení pomocí služby Azure IoT Hub (Node) | Microsoft Docs
description: Postup posílání zpráv z cloudu na zařízení ze služby Azure IoT Hub pomocí sad SDK Azure IoT pro Node. js. Aplikaci simulovaného zařízení upravíte tak, aby přijímala zprávy z cloudu na zařízení a upravila back-end aplikaci pro posílání zpráv z cloudu na zařízení.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: d3e4e0f4e7b1f8d3e100b3f1b3446907cfd587c5
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716944"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Posílání zpráv z cloudu na zařízení pomocí IoT Hub (Node. js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Úvod

Azure IoT Hub je plně spravovaná služba, která pomáhá zajistit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. V rychlém startu [pro odeslání ze zařízení do služby IoT Hub](quickstart-send-telemetry-node.md) se dozvíte, jak vytvořit centrum IoT, zřídit v něm identitu zařízení a zakódovat aplikaci simulovaného zařízení, která odesílá zprávy typu zařízení-Cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz sestaví na [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-node.md). Ukazuje, jak:

* Z back-endu vašeho řešení odesílají zprávy typu cloud-zařízení do jediného zařízení prostřednictvím IoT Hub.
* Příjem zpráv typu cloud-zařízení na zařízení.
* Z back-endu vašeho řešení požadavek na doručení zprávodesílaných do zařízení z IoT Hub.

Další informace o zprávách z cloudu na zařízení najdete v [příručce pro vývojáře IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto kurzu spustíte dvě konzolové aplikace Node. js:

* **SimulatedDevice**upravenou verzi aplikace vytvořenou v části [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-node.md), která se připojí ke službě IoT Hub a přijímá zprávy typu cloud-zařízení.

* **SendCloudToDeviceMessage**, která pošle zprávu typu cloud-zařízení do aplikace simulovaného zařízení prostřednictvím IoT Hub a potom obdrží potvrzení o doručení.

> [!NOTE]
> IoT Hub podporuje sadu SDK pro mnoho platforem a jazyků zařízení (včetně C, Java, Pythonu a JavaScriptu) prostřednictvím sad SDK pro zařízení Azure IoT. Podrobné pokyny, jak připojit zařízení k kódu tohoto kurzu a obecně k Azure IoT Hub, najdete v [centru pro vývojáře Azure IoT](https://azure.microsoft.com/develop/iot).
>

Pro absolvování tohoto kurzu potřebujete:

* Node. js verze 10.0. x nebo novější.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial) .)

## <a name="receive-messages-in-the-simulated-device-app"></a>Přijímání zpráv v aplikaci simulovaného zařízení

V této části upravíte aplikaci simulovaného zařízení, kterou jste vytvořili v části [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-node.md) pro příjem zpráv z cloudu na zařízení ze služby IoT Hub.

1. Pomocí textového editoru otevřete soubor **SimulatedDevice. js** . Tento soubor se nachází ve složce **IoT-hub\Quickstarts\simulated-Device** v kořenovém adresáři ukázkového kódu Node. js, který jste stáhli v rámci [odesílání telemetrie ze zařízení do rychlého startu centra IoT](quickstart-send-telemetry-node.md) .

2. Zaregistrujte obslužnou rutinu u klienta zařízení, aby přijímala zprávy odesílané z IoT Hub. Přidejte volání do `client.on` hned za řádek, který vytvoří klienta zařízení, jako v následujícím fragmentu kódu:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    V tomto příkladu zařízení vyvolá funkci **Complete** , aby upozornila IoT Hub, že zprávu zpracoval. Volání metody **Complete** není vyžadováno, pokud používáte přenos MQTT a lze je vynechat. Vyžaduje se pro HTTPS a AMQP.
  
   > [!NOTE]
   > Pokud jako přenos použijete HTTPS místo MQTT nebo AMQP, zkontroluje instance **DeviceClient** zprávy z IoT Hub zřídka (méně než každých 25 minut). Další informace o rozdílech mezi MQTT, AMQP a podporou protokolu HTTPS a omezením IoT Hub najdete v příručce pro [vývojáře IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu pro posílání zpráv z cloudu na zařízení prostřednictvím služby IoT Hub, kterou jste vytvořili v tématu [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-node.md). K posílání zpráv z cloudu na zařízení potřebuje služba oprávnění **připojit** se. Ve výchozím nastavení se všechny IoT Hub vytvoří pomocí zásad sdíleného přístupu s názvem **Služba** , která toto oprávnění udělí.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud-zařízení

V této části vytvoříte konzolovou aplikaci Node. js, která posílá zprávy typu cloud-zařízení do aplikace simulovaného zařízení. Budete potřebovat ID zařízení, které jste přidali v části [odeslání telemetrie ze zařízení do rychlého startu služby IoT Hub](quickstart-send-telemetry-node.md) . Také potřebujete připojovací řetězec centra IoT, který jste zkopírovali dříve, v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).

1. Vytvořte prázdnou složku s názvem **sendcloudtodevicemessage**. Ve složce **sendcloudtodevicemessage** vytvořte soubor Package. JSON pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:

    ```shell
    npm init
    ```

2. Na příkazovém řádku ve složce **sendcloudtodevicemessage** spusťte následující příkaz k instalaci balíčku **Azure-iothub** :

    ```shell
    npm install azure-iothub --save
    ```

3. Pomocí textového editoru vytvořte ve složce **SendCloudToDeviceMessage** soubor **SendCloudToDeviceMessage. js** .

4. Na začátek souboru `require` **SendCloudToDeviceMessage. js** přidejte následující příkazy:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Do souboru **SendCloudToDeviceMessage. js** přidejte následující kód. Nahraďte zástupné hodnoty {IoT Hub Connection String} a {ID zařízení} připojovacím řetězcem centra IoT a ID zařízení, které jste si poznamenali dříve:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Přidejte následující funkci pro tisk výsledků operace do konzoly:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Přidejte následující funkci, která vytiskne zprávy zpětné vazby doručení do konzoly:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Přidejte následující kód, který odešle zprávu do zařízení a zpracuje zprávu o zpětné vazbě, když zařízení potvrdí zprávu typu cloud-zařízení:

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

9. Uložte a zavřete soubor **SendCloudToDeviceMessage. js** .

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Na příkazovém řádku ve složce **simulovaného zařízení** spusťte následující příkaz k odeslání telemetrie do IoT Hub a pro poslech zpráv z cloudu na zařízení:

    ```shell
    node SimulatedDevice.js
    ```

    ![Spuštění aplikace simulovaného zařízení](./media/iot-hub-node-node-c2d/receivec2d.png)

2. Na příkazovém řádku ve složce **sendcloudtodevicemessage** spusťte následující příkaz pro odeslání zprávy ze zařízení do cloudu a počkejte na odezvu na potvrzení:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Spuštění aplikace pro odeslání příkazu Cloud-zařízení](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Pro zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo v článku, [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak odesílat a přijímat zprávy z cloudu do zařízení.

Příklady kompletních řešení, která používají IoT Hub, najdete v tématu [akcelerátor řešení vzdáleného monitorování Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Další informace o vývoji řešení pomocí IoT Hub najdete v příručce pro [vývojáře IoT Hub](iot-hub-devguide.md).
