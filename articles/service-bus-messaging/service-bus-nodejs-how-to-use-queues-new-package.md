---
title: Použití front azure/service-bus v souboru Node.js
description: Zjistěte, jak napsat program Nodejs pro odesílání zpráv a přijímání @azure/service-bus zpráv z fronty služby Service Bus pomocí nového balíčku.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330647"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Úvodní příručka: Použití front service bus u souboru Node.js a balíčku azure/service-bus
V tomto kurzu se dozvíte, jak napsat program Nodejs pro odesílání zpráv a [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) přijímat zprávy z fronty service bus pomocí nového balíčku. Tento balíček používá rychlejší [protokol AMQP 1.0](service-bus-amqp-overview.md) vzhledem k tomu, že starší [balíček azure-sb](https://www.npmjs.com/package/azure-sb) používá [run-time API service bus REST](/rest/api/servicebus/service-bus-runtime-rest). Ukázky jsou napsány v Jazyce JavaScript.

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat [výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Pokud nemáte frontu pro práci s, postupujte podle kroků na [portálu Azure k vytvoření](service-bus-quickstart-portal.md) článku fronty Service Bus k vytvoření fronty. Poznamenejte si připojovací řetězec pro instanci service bus a název fronty, kterou jste vytvořili. Použijeme tyto hodnoty ve vzorcích.

> [!NOTE]
> - Tento kurz funguje s ukázkami, které můžete kopírovat a spouštět pomocí [Nodejs](https://nodejs.org/). Pokyny k vytvoření aplikace Node.js najdete v tématu [Vytvoření a nasazení aplikace Node.js na web Azure](../app-service/app-service-web-get-started-nodejs.md)nebo [Node.js cloudové služby pomocí prostředí Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Nový [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) balíček ještě nepodporuje vytváření front. Pokud je [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) chcete programově vytvořit, použijte balíček.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalace balíčku pomocí Node Package Manageru (NPM)
Chcete-li nainstalovat balíček npm pro službu `npm` Service Bus, otevřete příkazový řádek, který má v cestě, změňte adresář na složku, ve které chcete mít vzorky, a spusťte tento příkaz.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Interakce s frontou služby Service Bus začíná vytvořením instance třídy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) a její použitím k vytvoření instance třídy [QueueClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) Jakmile máte klienta fronty, můžete vytvořit odesílatele a použít buď [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) nebo [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metoda na něj odesílat zprávy.

1. Otevření oblíbeného editoru, například [Kódu Visual Studia](https://code.visualstudio.com/)
2. Vytvořte soubor `send.js` s názvem a vložte do něj níže uvedený kód. Tento kód odešle do fronty 10 zpráv.

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
3. Do výše uvedeného kódu zadejte připojovací řetězec a název fronty.
4. Potom spusťte příkaz `node send.js` v příkazovém řádku a spusťte tento soubor.

Blahopřejeme! Právě jste odeslali zprávy do fronty služby Service Bus.

Zprávy mají některé `label` standardní `messageId` vlastnosti, jako je, které můžete nastavit při odesílání. Pokud chcete nastavit všechny vlastní vlastnosti, použijte `userProperties`, což je objekt json, který může obsahovat dvojice klíč-hodnota vlastních dat.

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Počet zpráv uchovávaných ve frontě není nijak omezen, ale je omezena celková velikost zpráv držených frontou. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách naleznete v [tématu Kvóty služby Service Bus](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty
Interakce s frontou služby Service Bus začíná vytvořením instance třídy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) a její použitím k vytvoření instance třídy [QueueClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) Jakmile máte klienta fronty, můžete vytvořit příjemce a použít buď [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) nebo [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metoda na něm přijímat zprávy.

1. Otevření oblíbeného editoru, například [Kódu Visual Studia](https://code.visualstudio.com/)
2. Vytvořte soubor `recieve.js` s názvem a vložte do něj níže uvedený kód. Tento kód se pokusí přijímat 10 zpráv z fronty. Skutečný počet, který obdržíte, závisí na počtu zpráv ve frontě a latenci sítě.

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
3. Do výše uvedeného kódu zadejte připojovací řetězec a název fronty.
4. Potom spusťte příkaz `node receiveMessages.js` v příkazovém řádku a spusťte tento soubor.

Blahopřejeme! Právě jste obdrželi zprávy z fronty služby Service Bus.

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) Metoda trvá `ReceiveMode` v což je výčtu s hodnotami [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) a [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Nezapomeňte [vyrovnat zprávy,](message-transfers-locks-settlement.md#settling-receive-operations) pokud `PeekLock` používáte režim `complete()` `abandon()`pomocí `defer()`některého z , , , nebo `deadletter()` metody na zprávu.

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Další informace naleznete v následujících zdrojích informací.
- [Fronty, témata a odběry](service-bus-queues-topics-subscriptions.md)
- Pokladna další [ukázky Nodejs pro service bus na GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Středisko pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/)

