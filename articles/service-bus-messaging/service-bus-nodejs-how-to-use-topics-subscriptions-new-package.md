---
title: Použití témat a předplatných azure/service-bus pomocí souboru Node.js
description: 'Úvodní příručka: Naučte se používat témata a předplatná služby Service Bus v Azure z aplikace Node.js.'
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 6088b4c54ed16c5ef46d2c0671e619884cad29d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330613"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Úvodní příručka: Použití témat a předplatných služby Service Bus pomocí souboru Node.js a balíčku azure/service-bus
V tomto kurzu se dozvíte, jak napsat program Node.js pro odesílání zpráv do tématu [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) service bus a přijímat zprávy z předplatného služby Service Bus pomocí nového balíčku. Tento balíček používá rychlejší [protokol AMQP 1.0](service-bus-amqp-overview.md) vzhledem k tomu, že starší [balíček azure-sb](https://www.npmjs.com/package/azure-sb) používá [run-time API service bus REST](/rest/api/servicebus/service-bus-runtime-rest). Ukázky jsou napsány v Jazyce JavaScript.

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat [výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Pokud nemáte téma a předplatné pro práci s, postupujte podle kroků na [portálu Azure k vytvoření témat service bus a odběrů](service-bus-quickstart-topics-subscriptions-portal.md) článku k jejich vytvoření. Poznamenejte si připojovací řetězec pro instanci service bus a názvy tématu a předplatného, které jste vytvořili. Použijeme tyto hodnoty ve vzorcích.

> [!NOTE]
> - Tento kurz funguje s ukázkami, které můžete kopírovat a spouštět pomocí [Nodejs](https://nodejs.org/). Pokyny k vytvoření aplikace Node.js najdete v tématu [Vytvoření a nasazení aplikace Node.js na web Azure](../app-service/app-service-web-get-started-nodejs.md)nebo [Node.js Cloud Service pomocí prostředí Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Nový [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) balíček ještě nepodporuje vytváření topcis a předplatného. Pokud je [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) chcete programově vytvořit, použijte balíček.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalace balíčku pomocí Node Package Manageru (NPM)
Chcete-li nainstalovat balíček npm pro službu `npm` Service Bus, otevřete příkazový řádek, který má v cestě, změňte adresář na složku, ve které chcete mít vzorky, a spusťte tento příkaz.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
Interakce s tématem service bus začíná vytvořením instance třídy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) a jeho použitím k vytvoření instance třídy [TopicClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) Jakmile máte klienta tématu, můžete vytvořit odesílatele a použít buď [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) nebo [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metodu na něj odesílat zprávy.

1. Otevření oblíbeného editoru, například [Kódu Visual Studia](https://code.visualstudio.com/)
2. Vytvořte soubor `send.js` s názvem a vložte do něj níže uvedený kód. Tento kód pošle 10 zpráv na vaše téma.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Do výše uvedeného kódu zadejte připojovací řetězec a název tématu.
4. Potom spusťte příkaz `node send.js` v příkazovém řádku a spusťte tento soubor. 

Blahopřejeme! Právě jste odeslali zprávy do fronty služby Service Bus.

Zprávy mají některé `label` standardní `messageId` vlastnosti, jako je, které můžete nastavit při odesílání. Pokud chcete nastavit všechny vlastní vlastnosti, použijte `userProperties`, což je objekt json, který může obsahovat dvojice klíč-hodnota vlastních dat.

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Počet zpráv v tématu není nijak omezen, ale je omezena celková velikost zpráv, které se v tématu uchovává. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách naleznete v [tématu Kvóty služby Service Bus](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z předplatného
Interakce s odběr service bus začíná vytváření min. [třídy ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) a jeho použití k vytvoření instance třídy [SubscriptionClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) Jakmile máte klienta předplatného, můžete vytvořit příjemce a použít buď [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) nebo [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metodu na něm přijímat zprávy.

1. Otevření oblíbeného editoru, například [Kódu Visual Studia](https://code.visualstudio.com/)
2. Vytvořte soubor `recieve.js` s názvem a vložte do něj níže uvedený kód. Tento kód se pokusí přijímat 10 zpráv z vašeho předplatného. Skutečný počet, který obdržíte, závisí na počtu zpráv v předplatném a latenci sítě.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Do výše uvedeného kódu zadejte připojovací řetězec a názvy tématu a předplatného.
4. Potom spusťte příkaz `node receiveMessages.js` v příkazovém řádku a spusťte tento soubor.

Blahopřejeme! Právě jste obdrželi zprávy z předplatného služby Service Bus.

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) Metoda trvá `ReceiveMode` v což je výčtu s hodnotami [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) a [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Nezapomeňte [vyrovnat zprávy,](message-transfers-locks-settlement.md#settling-receive-operations) pokud `PeekLock` používáte režim `complete()` `abandon()`pomocí `defer()`některého z , , , nebo `deadletter()` metody na zprávu.

## <a name="subscription-filters-and-actions"></a>Filtry a akce předplatného
Service Bus podporuje [filtry a akce na odběry](topic-filters.md), který umožňuje filtrovat příchozí zprávy do předplatného a upravit jejich vlastnosti.

Jakmile máte instanci, `SubscriptionClient` můžete použít níže uvedené metody k získání, přidání a odebrání pravidel předplatného pro řízení filtrů a akcí.

- getRules
- Addrule
- odebratpravidlo

Každé předplatné má výchozí pravidlo, které používá filtr true k povolení všech příchozích zpráv. Když přidáte nové pravidlo, nezapomeňte odebrat výchozí filtr, aby filtr v novém pravidle fungoval. Pokud předplatné nemá žádná pravidla, neobdrží žádné zprávy.

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Další informace naleznete v následujících zdrojích informací.

- [Fronty, témata a odběry](service-bus-queues-topics-subscriptions.md)
- Pokladna další [ukázky Nodejs pro service bus na GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Středisko pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/)


