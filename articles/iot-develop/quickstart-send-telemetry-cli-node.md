---
title: Odesílání telemetrie zařízení do služby Azure IoT Hub rychlý Start (Node.js)
description: V tomto rychlém startu použijete sadu Azure IoT Hub Device SDK pro Node.js k odeslání telemetrie ze zařízení do služby IoT Hub.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 97fcff4706d6da968c93426f85569fed9af95aac
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197805"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub (Node.js)

**Platí pro**: [vývoj aplikací pro zařízení](about-iot-develop.md#device-application-development)

V tomto rychlém startu se naučíte základní pracovní postup vývoje aplikací pro zařízení IoT. Pomocí Azure CLI vytvoříte Azure IoT Hub a simulované zařízení, potom pro přístup k zařízení a odeslání telemetrie do centra použijete sadu Azure IoT Node.js SDK.

## <a name="prerequisites"></a>Požadavky
- Pokud ještě nemáte předplatné Azure, [vytvořte ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ještě před tím, než začnete.
- Azure CLI Všechny příkazy v tomto rychlém startu můžete spustit pomocí Azure Cloud Shell interaktivního prostředí CLI, které běží v prohlížeči. Pokud používáte Cloud Shell, nemusíte nic instalovat. Pokud dáváte přednost používání rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.76 nebo novější. Pokud chcete zjistit verzi, spusťte příkaz az --version. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).
- [Node.js 10 +](https://nodejs.org). Pokud používáte Azure Cloud Shell, neaktualizujte nainstalovanou verzi Node.js. Azure Cloud Shell už má nejnovější Node.js verzi.

    Ověřte aktuální verzi Node.js na vývojovém počítači pomocí následujícího příkazu:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Odesílání zpráv pomocí sady Node.js SDK
V této části použijete sadu SDK Node.js k posílání zpráv ze simulovaného zařízení do služby IoT Hub. 

1. Otevřete nové okno terminálu. Pomocí tohoto terminálu nainstalujete sadu Node.js SDK a budete pracovat s ukázkovým kódem Node.js. Nyní byste měli mít otevřený dva terminály: tu, kterou jste právě otevřeli pro práci se Node.js, a prostředí CLI, které jste použili v předchozích částech, k zadávání příkazů rozhraní příkazového řádku Azure CLI. 

1. Zkopírujte [ukázky zařízení Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) do místního počítače:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Přejděte do adresáře *Azure-IoT-SDK-Node/Device/Samples* :

    ```console
    cd azure-iot-sdk-node/device/samples
    ```
1. Nainstalujte sadu Azure IoT Node.js SDK a nezbytné závislosti:

    ```console
    npm install
    ```
    Tento příkaz nainstaluje správné závislosti uvedené v souboru *package.json* v adresáři ukázek zařízení.

1. Nastavte řetězec připojení zařízení jako proměnnou prostředí s názvem `DEVICE_CONNECTION_STRING` . Hodnota řetězce, která se má použít, je řetězec, který jste získali v předchozí části po vytvoření simulovaného Node.js zařízení. 

    **Windows (cmd)**

    ```console
    set DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > Pro Windows CMD nejsou kolem připojovacího řetězce žádné uvozovky.

    **Linux (bash)**

    ```bash
    export DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. V otevřeném prostředí CLI spusťte příkaz [AZ IoT Hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) a začněte monitorovat události na simulovaném zařízení IoT.  Zprávy o událostech budou vytištěny v terminálu po doručení.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. V Node.js terminálu spusťte kód instalovaného ukázkového souboru *simple_sample_device.js* . Tento kód přistupuje k simulovanému zařízení IoT a pošle zprávu do centra IoT.

    Spuštění ukázky Node.js z terminálu:
    ```console
    node ./simple_sample_device.js
    ```

    Volitelně můžete spustit kód Node.js z ukázky v JavaScriptu JavaScript:
    ```javascript
    'use strict';

    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    // Uncomment one of these transports and then change it in fromConnectionString to test other transports
    // const Protocol = require('azure-iot-device-amqp').AmqpWs;
    // const Protocol = require('azure-iot-device-http').Http;
    // const Protocol = require('azure-iot-device-amqp').Amqp;
    // const Protocol = require('azure-iot-device-mqtt').MqttWs;
    const Client = require('azure-iot-device').Client;
    const Message = require('azure-iot-device').Message;

    // String containing Hostname, Device Id & Device Key in the following formats:
    //  "HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    let sendInterval;

    function disconnectHandler () {
    clearInterval(sendInterval);
    client.open().catch((err) => {
        console.error(err.message);
    });
    }

    // The AMQP and HTTP transports have the notion of completing, rejecting or abandoning the message.
    // For example, this is only functional in AMQP and HTTP:
    // client.complete(msg, printResultFor('completed'));
    // If using MQTT calls to complete, reject, or abandon are no-ops.
    // When completing a message, the service that sent the C2D message is notified that the message has been processed.
    // When rejecting a message, the service that sent the C2D message is notified that the message won't be processed by the device. the method to use is client.reject(msg, callback).
    // When abandoning the message, IoT Hub will immediately try to resend it. The method to use is client.abandon(msg, callback).
    // MQTT is simpler: it accepts the message by default, and doesn't support rejecting or abandoning a message.
    function messageHandler (msg) {
    console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
    client.complete(msg, printResultFor('completed'));
    }

    function generateMessage () {
    const windSpeed = 10 + (Math.random() * 4); // range: [10, 14]
    const temperature = 20 + (Math.random() * 10); // range: [20, 30]
    const humidity = 60 + (Math.random() * 20); // range: [60, 80]
    const data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed, temperature: temperature, humidity: humidity });
    const message = new Message(data);
    message.properties.add('temperatureAlert', (temperature > 28) ? 'true' : 'false');
    return message;
    }

    function errorCallback (err) {
    console.error(err.message);
    }

    function connectCallback () {
    console.log('Client connected');
    // Create a message and send it to the IoT Hub every two seconds
    sendInterval = setInterval(() => {
        const message = generateMessage();
        console.log('Sending message: ' + message.getData());
        client.sendEvent(message, printResultFor('send'));
    }, 2000);

    }

    // fromConnectionString must specify a transport constructor, coming from any transport package.
    let client = Client.fromConnectionString(deviceConnectionString, Protocol);

    client.on('connect', connectCallback);
    client.on('error', errorCallback);
    client.on('disconnect', disconnectHandler);
    client.on('message', messageHandler);

    client.open()
    .catch(err => {
    console.error('Could not connect: ' + err.message);
    });

    // Helper function to print results in the console
    function printResultFor(op) {
    return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
    };
    }
    ```

Jelikož kód Node.js odesílá simulovanou zprávu telemetrie ze zařízení do služby IoT Hub, zobrazí se tato zpráva v prostředí CLI, které sleduje události:

```output
event:
  component: ''
  interface: ''
  module: ''
  origin: <your device name>
  payload: '{"deviceId":"myFirstDevice","windSpeed":11.853592092144627,"temperature":22.62484121157508,"humidity":66.17960805575937}'
```

Vaše zařízení je teď bezpečně připojené a odesílá telemetrii do Azure IoT Hub.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete prostředky Azure vytvořené v rámci tohoto rychlého startu, můžete je odstranit pomocí Azure CLI.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. 

Odstranění skupiny prostředků podle názvu:
1. Spusťte příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) . Tento příkaz odebere skupinu prostředků, IoT Hub a registraci zařízení, kterou jste vytvořili.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Spuštěním příkazu [AZ Group list](/cli/azure/group#az-group-list) potvrďte odstranění skupiny prostředků.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili základní pracovní postup aplikace Azure IoT pro bezpečné připojení zařízení ke cloudu a odesílání telemetrie typu zařízení-Cloud. Pomocí Azure CLI jste vytvořili centrum IoT a simulované zařízení. potom jste použili sadu Azure IoT Node.js SDK pro přístup k zařízení a odesílání telemetrie do centra. 

V dalším kroku Prozkoumejte sadu Azure IoT Node.js SDK prostřednictvím ukázek aplikací.

- [Další Node.js ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): Tento adresář obsahuje další ukázky z úložiště Node.js SDK, které prezentují IoT Hub scénáře.