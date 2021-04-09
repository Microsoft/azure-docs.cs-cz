---
title: Použití náhledu JavaScriptu Azure/Service-Bus s tématy a předplatnými
description: Naučte se psát program JavaScriptu, který používá nejnovější verzi Preview @azure/service-bus balíčku k odesílání zpráv do Service Bus tématu a příjem zpráv z odběru tématu.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: a1afe4207ce3833f3bcb55bc7bc2e8e27f393f63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179992"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Rychlý Start: Service Bus témata a předplatná pomocí Node.js a balíčku Azure/Service-Bus verze Preview
V tomto kurzu se naučíte používat [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) balíček v programu JavaScriptu k posílání zpráv do Service Busho tématu a přijímání zpráv z Service Bus předplatného do tohoto tématu.

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Postupujte podle kroků v [rychlém startu: pomocí Azure Portal vytvořte Service Bus téma a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md). Poznamenejte si připojovací řetězec, název tématu a název předplatného. Pro tento rychlý Start budete používat jenom jedno předplatné. 

> [!NOTE]
> - Tento kurz funguje s ukázkami, které můžete zkopírovat a spustit pomocí [NodeJS](https://nodejs.org/). Pokyny k vytvoření Node.js aplikace najdete v tématu [Vytvoření a nasazení Node.js aplikace na web Azure](../app-service/quickstart-nodejs.md)nebo [Node.js cloudovou službu pomocí Windows PowerShellu](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalace balíčku pomocí Node Package Manageru (NPM)
Chcete-li nainstalovat balíček npm pro Service Bus, otevřete příkazový řádek, který má `npm` v cestě, změňte adresář na složku, ve které chcete mít ukázky, a pak spusťte tento příkaz.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
Následující vzorový kód ukazuje, jak odeslat dávku zpráv do Service Bus tématu. Podrobnosti najdete v tématu komentáře ke kódu. 

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte soubor s názvem `sendtotopic.js` a vložte do něj následující kód. Tento kód pošle zprávu do vašeho tématu.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createMessageBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });    
    ```
3. Nahraďte `<SERVICE BUS NAMESPACE CONNECTION STRING>` připojovacím řetězcem k vašemu oboru názvů Service Bus.
1. Nahraďte `<TOPIC NAME>` názvem tématu. 
1. Potom spuštěním příkazu na příkazovém řádku spusťte tento soubor.

    ```console
    node sendtotopic.js 
    ```
1. Měli byste vidět následující výstup.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Přijímání zpráv z předplatného
1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte soubor s názvem **receivefromsubscription.js** a vložte do něj následující kód. Podrobnosti najdete v tématu komentáře ke kódu. 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });    
    ```
3. Nahraďte `<SERVICE BUS NAMESPACE CONNECTION STRING>` připojovacím řetězcem k oboru názvů. 
1. Nahraďte `<TOPIC NAME>` názvem tématu. 
1. Nahraďte `<SUBSCRIPTION NAME>` názvem předplatného tématu. 
1. Potom spuštěním příkazu na příkazovém řádku spusťte tento soubor.

    ```console
    node receivefromsubscription.js
    ```
1. Měli byste vidět následující výstup.

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

V Azure Portal přejděte na obor názvů Service Bus a v dolním podokně vyberte téma, kde se zobrazí stránka **Service Bus téma** pro vaše téma. Na této stránce byste měli vidět tři příchozí a tři odchozí zprávy v grafu **zprávy** . 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Příchozí a odchozí zprávy":::

Pokud spustíte jenom aplikaci odeslat, na stránce **Service Bus téma** uvidíte šest příchozích zpráv (3 nové), ale tři odchozí zprávy. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Stránka aktualizovaného tématu":::

Po výběru předplatného na této stránce se dostanete na stránku **Service Bus předplatné** . Na této stránce můžete zobrazit počet aktivních zpráv, počet nedoručených zpráv a další. V tomto příkladu jsou k dispozici tři aktivní zprávy, které příjemce ještě nepřijal. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Počet aktivních zpráv":::

## <a name="next-steps"></a>Další kroky
Podívejte se na následující dokumentaci a ukázky: 

- [Klientská knihovna Azure Service Bus pro Python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). Složka **JavaScriptu** obsahuje ukázky JavaScriptu a v **TypeScript** jsou ukázky TypeScriptu. 
- [Referenční dokumentace k Azure-ServiceBus](/javascript/api/overview/azure/service-bus)
