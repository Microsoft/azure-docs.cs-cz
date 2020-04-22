---
title: Zprávy z cloudu na zařízení s Azure IoT Hub (Node) | Dokumenty společnosti Microsoft
description: Jak odesílat zprávy z cloudu na zařízení do zařízení z centra Azure IoT pomocí sad Azure IoT SDK pro Node.js. Můžete upravit simulované zařízení aplikace přijímat zprávy z cloudu na zařízení a upravit back-end aplikace pro odesílání zpráv z cloudu na zařízení.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a1e0e3623692321e5c69e4b9c5a26ff82a1c47a0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732345"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Odesílání zpráv z cloudu na zařízení pomocí služby IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá umožnit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endem řešení. [Odeslat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md) rychlý start ukazuje, jak vytvořit službu IoT hub, zřízení identity zařízení v něm a kód simulované zařízení aplikace, která odesílá zprávy zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz staví na [odesílání telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md). To vám ukáže, jak:

* Z back-endu vašeho řešení můžete odesílat zprávy z cloudu na zařízení do jednoho zařízení prostřednictvím služby IoT Hub.
* Příjem zpráv z cloudu na zařízení na zařízení.
* Z back-endu vašeho řešení požádejte o potvrzení doručení *(zpětnou vazbu)* pro zprávy odeslané do zařízení ze služby IoT Hub.

Další informace o zprávách mezi cloudy najdete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto kurzu spustíte dvě konzolové aplikace Node.js:

* **SimulatedDevice**, upravená verze aplikace vytvořená v [send telemetrii ze zařízení do centra IoT hub](quickstart-send-telemetry-node.md), který se připojuje k centru IoT hub a přijímá zprávy z cloudu na zařízení.

* **SendCloudToDeviceMessage**, který odešle zprávu z cloudu na zařízení do aplikace simulované zařízení prostřednictvím služby IoT Hub a pak obdrží potvrzení o doručení.

> [!NOTE]
> IoT Hub má podporu sady SDK pro mnoho platforem a jazyků zařízení (včetně C, Java, Pythonu a Javascriptu) prostřednictvím sad SDK zařízení Azure IoT. Podrobné pokyny, jak připojit zařízení ke kódu tohoto kurzu a obecně k azure iot hubu, najdete v [tématu Azure IoT Developer Center](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Požadavky

* Node.js verze 10.0.x nebo novější. [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) popisuje, jak nainstalovat soubor Node.js pro tento kurz v systému Windows nebo Linux.

* Aktivní účet Azure. (Pokud nemáte účet, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial) během několika minut.)

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Příjem zpráv v aplikaci pro simulované zařízení

V této části upravíte aplikaci simulovaných zařízení, kterou jste vytvořili v [aplikaci Send telemetrie ze zařízení do centra IoT,](quickstart-send-telemetry-node.md) abyste přijímali zprávy mezi cloudy z centra IoT.

1. Pomocí textového editoru otevřete soubor **SimulatedDevice.js.** Tento soubor je umístěn ve složce **iot-hub\Quickstarts\simulated-device** mimo kořenovou složku ukázkového kódu Node.js, který jste stáhli v [aplikaci Odeslat telemetrii ze zařízení do rychlého startu centra IoT.](quickstart-send-telemetry-node.md)

2. Zaregistrujte obslužnou rutinu s klientem zařízení pro příjem zpráv odeslaných z centra IoT Hub. Přidejte volání `client.on` těsně za řádek, který vytvoří klienta zařízení jako v následujícím fragmentu:

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

    V tomto příkladu zařízení vyvolá **úplnou** funkci upozornit Službu IoT Hub, že zprávu zpracovalo. Volání **dokončení** není vyžadováno, pokud používáte přenos MQTT a může být vynecháno. Je vyžadovánpro protokoly HTTPS a AMQP.
  
   > [!NOTE]
   > Pokud používáte https místo MQTT nebo AMQP jako přenos, **deviceclient** instance kontroluje zprávy z ioT hubu zřídka (méně než každých 25 minut). Další informace o rozdílech mezi podporou MQTT, AMQP a HTTPS a omezením služby IoT Hub naleznete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

V tomto článku vytvoříte back-endovou službu pro odesílání zpráv mezi cloudy prostřednictvím služby IoT hub, kterou jste vytvořili v [aplikaci Send telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md). K odesílání zpráv z cloudu na zařízení potřebuje vaše služba oprávnění **k připojení služby.** Ve výchozím nastavení je každé centrum IoT Hub vytvořeno pomocí zásady sdíleného přístupu s názvem **služba,** která uděluje toto oprávnění.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy z cloudu na zařízení

V této části vytvoříte konzolovou aplikaci Node.js, která odesílá zprávy z cloudu na zařízení do aplikace simulovaných zařízení. Potřebujete ID zařízení zařízení, které jste přidali v [odesílání telemetrie ze zařízení do rychlého startu služby IoT hub.](quickstart-send-telemetry-node.md) Potřebujete také připojovací řetězec centra IoT, který jste dříve zkopírovali v [připojovacím řetězci Konzumního Centrum IoT](#get-the-iot-hub-connection-string).

1. Vytvořte prázdnou složku s názvem **sendcloudtodevicemessage**. Ve složce **sendcloudtodevicemessage** vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:

    ```shell
    npm init
    ```

2. Na příkazovém řádku ve složce **sendcloudtodevicemessage** spusťte následující příkaz pro instalaci balíčku **azure-iothub:**

    ```shell
    npm install azure-iothub --save
    ```

3. Pomocí textového editoru vytvořte soubor **SendCloudToDeviceMessage.js** ve složce **sendcloudtodevicemessage.**

4. Přidejte `require` následující příkazy na začátku souboru **SendCloudToDeviceMessage.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Přidejte následující kód do souboru **SendCloudToDeviceMessage.js.** Nahraďte zástupné hodnoty "{iot hub connection string}" a {device id}zástupným symbolem připojovacím řetězcem ioT hubu a ID zařízení, které jste si dříve poznamenali:

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

7. Přidejte následující funkci pro tisk zpráv o zpětné vazbě doručení do konzoly:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Přidejte následující kód, který odešle zprávu do zařízení a zpracovat zprávu o zpětné vazbě, když zařízení potvrdí zprávu z cloudu na zařízení:

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

9. Uložit a zavřít **sendcloudtodevicemessage.js** soubor.

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Na příkazovém řádku ve složce **simulované zařízení** spusťte následující příkaz pro odeslání telemetrie do služby IoT Hub a naslouchání zprávám mezi cloudy a zařízeními:

    ```shell
    node SimulatedDevice.js
    ```

    ![Spuštění aplikace simulované zařízení](./media/iot-hub-node-node-c2d/receivec2d.png)

2. Na příkazovém řádku ve složce **sendcloudtodevicemessage** spusťte následující příkaz, který odešle zprávu z cloudu na zařízení a vyčkáte na zpětnou vazbu potvrzení:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Spuštění aplikace pro odeslání příkazu cloud-to-device](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Pro jednoduchost tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální backoff), jak je navrženo v článku [Přechodné zpracování chyb](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak odesílat a přijímat zprávy z cloudu na zařízení.

Příklady kompletních komplexních řešení, která používají službu IoT Hub, najdete v [tématu Akcelerátor řešení vzdáleného monitorování Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Další informace o vývoji řešení pomocí služby IoT Hub najdete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide.md).
