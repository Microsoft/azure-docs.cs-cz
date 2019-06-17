---
title: Jak používat fronty služby Azure Service Bus v Node.js – azure/service-bus | Dokumentace Microsoftu
description: Zjistěte, jak používat fronty Service Bus v Azure z aplikace Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 7aacefde9c037fcce64d9256e35082eb04e0a2f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988354"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Jak používat fronty služby Service Bus pomocí Node.js a balíček azure/service-bus
> [!div class="op_multi_selector" title1="Programovací jazyk" title2="Node.js balíčku"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

V tomto kurzu se dozvíte, jak psát Nodejs programu pro odesílání a příjem zpráv z fronty Service Bus pomocí nového [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) balíčku. Tento balíček používá rychlejší [protokolu AMQP 1.0](service-bus-amqp-overview.md) vzhledem k tomu starší [azure-sb](https://www.npmjs.com/package/azure-sb) balíček použitý [rozhraní API REST pro Service Bus za běhu](/rest/api/servicebus/service-bus-runtime-rest). Ukázky jsou napsané v jazyce JavaScript.

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete si aktivovat váš [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Pokud nemáte k dispozici pro práci s frontou, postupujte podle kroků v [použijte Azure portal můžete vytvořit frontu služby Service Bus](service-bus-quickstart-portal.md) článku můžete vytvořit frontu. Poznamenejte si připojovací řetězec pro vaši instanci služby Service Bus a název fronty, kterou jste vytvořili. Použijeme tyto hodnoty ve vzorcích.

> [!NOTE]
> - Tento kurz pracuje s ukázkami, které můžete zkopírovat a spustit pomocí [Nodejs](https://nodejs.org/). Pokyny o tom, jak vytvořit aplikaci v Node.js najdete v tématu [vytvoření a nasazení aplikace Node.js na web Azure](../app-service/app-service-web-get-started-nodejs.md), nebo [Node.js cloudové službě pomocí Windows Powershellu](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Nové [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) balíček nepodporuje vytváření front ještě. Použijte prosím [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) balíček, pokud chcete prostřednictvím kódu programu vytvořit.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalace balíčku pomocí Node Package Manageru (NPM)
Instalace balíčku npm služby Service Bus, otevřete příkazový řádek, který má `npm` v cestě, změňte adresář na složku, ve kterém chcete mít vašich ukázek a spusťte tento příkaz.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Interakce se službou Service Bus fronty začíná vytvoření instance [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) třídy a použít ho k vytvoření instance [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) třídy. Jakmile budete mít klienta fronty, můžete vytvořit odesílatele a použijte buď [odeslat](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) nebo [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metodu na ni odesílání zpráv.

1. Otevřete oblíbeného editoru, jako například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte soubor s názvem `send.js` a vložte níže uvedeného kódu do něj. Tento kód odešle 10 zpráv do fronty.

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
3. Zadejte připojovací řetězec a název vaší fronty ve výše uvedeném kódu.
4. Pak spusťte příkaz `node send.js` v příkazovém řádku a spusťte tento soubor.

Blahopřejeme! Jste právě odeslali zprávy do fronty služby Service Bus.

Zprávy mají některými standardní vlastnostmi, třeba `label` a `messageId` , kterou můžete nastavit při odesílání. Pokud chcete nastavit všechny vlastní vlastnosti, použijte `userProperties`, což je objekt json, který může obsahovat páry klíč hodnota vaše vlastní data.

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Neexistuje žádné omezení na počet zpráv držených ve frontě, ale celková velikost zpráv držených ve frontě je limitu. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách najdete v tématu [kvótách služby Service Bus](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty
Interakce se službou Service Bus fronty začíná vytvoření instance [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) třídy a použít ho k vytvoření instance [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) třídy. Jakmile budete mít klienta fronty, můžete vytvořit příjemce a použijte buď [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) nebo [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metoda ji pro příjem zpráv.

1. Otevřete oblíbeného editoru, jako například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte soubor s názvem `recieve.js` a vložte níže uvedeného kódu do něj. Tento kód se pokusí o 10 zpráv z fronty. Skutečný počet, který jste dostali závisí na počtu zpráv ve frontě a latenci.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.ReceiveAndDelete);
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
3. Zadejte připojovací řetězec a název vaší fronty ve výše uvedeném kódu.
4. Pak spusťte příkaz `node receiveMessages.js` v příkazovém řádku a spusťte tento soubor.

Blahopřejeme! Právě obdrželi zprávy z fronty Service Bus.

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) metoda přijímá `ReceiveMode` což je výčet s hodnotami [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) a [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Nezapomeňte [vyrovnat vaše zprávy](message-transfers-locks-settlement.md#settling-receive-operations) používáte `PeekLock` režimu pomocí některé z `complete()`, `abandon()`, `defer()`, nebo `deadletter()` metody ve zprávě.

> [!NOTE]
> Můžete spravovat prostředky služby Service Bus s [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Exploreru umožňuje uživatelům připojit k oboru názvů služby Service Bus a správě entit pro zasílání zpráv snadno způsobem. Tento nástroj nabízí pokročilé funkce, například funkce importu/exportu nebo možnost otestovat tématu, fronty, předplatná, služby pro přenos přes, notification hubs a centra událostí. 

## <a name="next-steps"></a>Další postup
Další informace naleznete v následujících zdrojích.
- [Fronty, témata a odběry](service-bus-queues-topics-subscriptions.md)
- Podívejte se na další [Nodejs ukázky pro Service Bus na Githubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Středisko pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/)

