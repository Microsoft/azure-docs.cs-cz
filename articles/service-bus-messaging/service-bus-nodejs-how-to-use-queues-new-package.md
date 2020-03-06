---
title: Jak používat fronty Azure/Service-Bus v Node. js
description: Naučte se psát program NodeJS, který odesílá zprávy do fronty Service Bus a přijímá je pomocí nového balíčku @azure/service-bus.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: c2e24e9dea2c8463294c85f04c9e4d7d2da17261
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330647"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Rychlý Start: jak používat Service Bus fronty pomocí Node. js a balíčku Azure/Service-Bus
V tomto kurzu se naučíte psát program NodeJS, který odesílá zprávy do fronty Service Bus a přijímá je pomocí nového balíčku [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) . Tento balíček používá rychlejší [Protokol AMQP 1,0](service-bus-amqp-overview.md) , zatímco starší balíček [Azure-SB](https://www.npmjs.com/package/azure-sb) používaný [Service Bus rozhraní API pro běh REST](/rest/api/servicebus/service-bus-runtime-rest). Ukázky jsou napsány v jazyce JavaScript.

## <a name="prerequisites"></a>Předpoklady
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Pokud nemáte frontu, ve které byste mohli pracovat, postupujte podle kroků v tématu [použití Azure Portal k vytvoření fronty Service Bus](service-bus-quickstart-portal.md) . Poznamenejte si připojovací řetězec pro vaši instanci Service Bus a název fronty, kterou jste vytvořili. Tyto hodnoty použijeme v ukázkách.

> [!NOTE]
> - Tento kurz funguje s ukázkami, které můžete zkopírovat a spustit pomocí [NodeJS](https://nodejs.org/). Pokyny k vytvoření aplikace Node. js najdete v tématu [Vytvoření a nasazení aplikace Node. js na web Azure](../app-service/app-service-web-get-started-nodejs.md)nebo [cloudovou službu Node. js pomocí prostředí Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Nový [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) balíček ještě nepodporuje vytváření front. Pokud je chcete programově vytvořit, použijte prosím balíček [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) .

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalace balíčku pomocí Node Package Manageru (NPM)
Chcete-li nainstalovat balíček npm pro Service Bus, otevřete příkazový řádek, který má v cestě `npm`, změňte adresář na složku, ve které chcete mít ukázky, a pak spusťte tento příkaz.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Interakce s Service Bus frontou začíná vytvořením instance třídy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) a jejím použitím k vytvoření instance třídy [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) . Jakmile budete mít klienta front, můžete vytvořit odesílatele a k odesílání zpráv použít buď metodu [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) , nebo [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) .

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte soubor s názvem `send.js` a vložte do něj následující kód. Tento kód pošle do fronty 10 zpráv.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Do výše uvedeného kódu zadejte připojovací řetězec a název vaší fronty.
4. Pak spusťte příkaz `node send.js` na příkazovém řádku, aby se tento soubor spustil.

Blahopřejeme! Právě jste odeslali zprávy do fronty Service Bus.

Zprávy mají některé standardní vlastnosti, například `label` a `messageId`, které můžete nastavit při odesílání. Pokud chcete nastavit jakékoli vlastní vlastnosti, použijte `userProperties`, což je objekt JSON, který může uchovávat páry klíč-hodnota vašich vlastních dat.

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Počet zpráv držených ve frontě není nijak omezený, ale celková velikost zpráv držených ve frontě je příliš velká. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách najdete v tématu [Service Bus kvóty](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Přijímání zpráv z fronty
Interakce s Service Bus frontou začíná vytvořením instance třídy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) a jejím použitím k vytvoření instance třídy [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) . Jakmile budete mít klienta front, můžete vytvořit přijímač a použít buď metodu [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) nebo [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) , pro příjem zpráv.

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte soubor s názvem `recieve.js` a vložte do něj následující kód. Tento kód se pokusí přijmout 10 zpráv z fronty. Skutečný počet, který obdržíte, závisí na počtu zpráv ve frontě a na latenci sítě.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Do výše uvedeného kódu zadejte připojovací řetězec a název vaší fronty.
4. Pak spusťte příkaz `node receiveMessages.js` na příkazovém řádku, aby se tento soubor spustil.

Blahopřejeme! Právě jste přijali zprávy z fronty Service Bus.

Metoda [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) přebírá v `ReceiveMode`, což je výčet s hodnotami [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) a [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Nezapomeňte [vyrovnávat zprávy](message-transfers-locks-settlement.md#settling-receive-operations) , pokud použijete režim `PeekLock` pomocí kterékoli z `complete()`, `abandon()`, `defer()`nebo `deadletter()` metod ve zprávě.

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Další informace najdete v následujících zdrojích informací.
- [Fronty, témata a odběry](service-bus-queues-topics-subscriptions.md)
- Rezervace dalších [ukázek NodeJS pro Service Bus na GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Středisku pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/)

