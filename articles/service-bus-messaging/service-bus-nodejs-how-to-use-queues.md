---
title: Jak používat fronty Azure/Service-Bus v JavaScriptu
description: Naučte se psát program JavaScriptu, který používá nejnovější verzi Preview @azure/service-bus balíčku k odesílání zpráv do a příjem zpráv z fronty Service Bus.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: ac24d84176f27170648545bc8044c5dcbc77781a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180009"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Odesílání zpráv do a příjem zpráv z Azure Service Busch front (JavaScript)
V tomto kurzu se naučíte používat [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) balíček v programu JavaScriptu k posílání a přijímání zpráv z fronty Service Bus.

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Pokud nemáte frontu, ve které byste mohli pracovat, postupujte podle kroků v tématu [použití Azure Portal k vytvoření fronty Service Bus](service-bus-quickstart-portal.md) . Poznamenejte si **připojovací řetězec** pro obor názvů Service Bus a název **fronty** , kterou jste vytvořili.

> [!NOTE]
> - Tento kurz funguje s ukázkami, které můžete zkopírovat a spustit pomocí [NodeJS](https://nodejs.org/). Pokyny k vytvoření Node.js aplikace najdete v tématu [Vytvoření a nasazení Node.js aplikace na web Azure](../app-service/quickstart-nodejs.md)nebo [Node.js cloudovou službu pomocí Windows PowerShellu](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalace balíčku pomocí Node Package Manageru (NPM)
Chcete-li nainstalovat balíček npm pro Service Bus, otevřete příkazový řádek, který má `npm` v cestě, změňte adresář na složku, ve které chcete mít ukázky, a pak spusťte tento příkaz.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Následující vzorový kód ukazuje, jak odeslat zprávu do fronty.

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com/).
2. Vytvořte soubor s názvem `send.js` a vložte do něj následující kód. Tento kód pošle zprávu do fronty. Zpráva má štítek (vědecký) a text (Einstein).

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
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
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the array            
    
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
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
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
3. Nahraďte `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` připojovacím řetězcem k vašemu oboru názvů Service Bus.
1. Nahraďte `<QUEUE NAME>` názvem fronty. 
1. Potom spuštěním příkazu na příkazovém řádku spusťte tento soubor.

    ```console
    node send.js 
    ```
1. Měl by se zobrazit následující výstup.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Přijímání zpráv z fronty

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte soubor s názvem `receive.js` a vložte do něj následující kód.

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
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
3. Nahraďte `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` připojovacím řetězcem k vašemu oboru názvů Service Bus.
1. Nahraďte `<QUEUE NAME>` názvem fronty. 
1. Potom spuštěním příkazu na příkazovém řádku spusťte tento soubor.

    ```console
    node receive.js
    ```
1. Měl by se zobrazit následující výstup.

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

Na stránce **Přehled** pro obor názvů Service Bus v Azure Portal můžete zobrazit počet **příchozích** a **odchozích** zpráv. Může se stát, že budete muset počkat na minutu nebo si aktualizovat stránku, aby se zobrazily nejnovější hodnoty. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Počet příchozích a odchozích zpráv":::

Na stránce s **přehledem** Vyberte frontu a přejděte na stránku **Service Bus Queue** . Na této stránce se zobrazuje počet **příchozích** a **odchozích** zpráv. Zobrazí se také další informace, jako je **aktuální velikost** fronty, **maximální velikost**, **Počet aktivních zpráv** a tak dále. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Podrobnosti fronty":::
## <a name="next-steps"></a>Další kroky
Podívejte se na následující dokumentaci a ukázky: 

- [Klientská knihovna Azure Service Bus pro JS](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). Složka **JavaScriptu** obsahuje ukázky JavaScriptu a v **TypeScript** jsou ukázky TypeScriptu. 
- [Referenční dokumentace k Azure-ServiceBus](/javascript/api/overview/azure/service-bus)
