---
title: Použití témat a předplatných Azure/Service-Bus s Node. js
description: 'Rychlý Start: Naučte se používat Service Bus témata a odběry v Azure z aplikace Node. js.'
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
ms.openlocfilehash: 348a6a50583594d3e608bb16fcef65879b595e67
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263315"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Rychlý Start: jak používat Service Bus témata a odběry s využitím Node. js a balíčku Azure/Service-Bus
> [!div class="op_multi_selector" title1="Programovací jazyk" title2="Manageru balíček s Node. js"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

V tomto kurzu zjistíte, jak napsat program Node. js pro posílání zpráv do Service Busho tématu a příjem zpráv z Service Bus předplatného pomocí nového balíčku [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) . Tento balíček používá rychlejší [Protokol AMQP 1,0](service-bus-amqp-overview.md) , zatímco starší balíček [Azure-SB](https://www.npmjs.com/package/azure-sb) používaný [Service Bus rozhraní API pro běh REST](/rest/api/servicebus/service-bus-runtime-rest). Ukázky jsou napsány v jazyce JavaScript.

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Pokud nemáte téma a předplatné, se kterým chcete pracovat, postupujte podle kroků v tématu [použití Azure Portal k vytvoření Service Bus témata a předplatných](service-bus-quickstart-topics-subscriptions-portal.md) , abyste je mohli vytvořit. Poznamenejte si připojovací řetězec pro vaši instanci Service Bus a názvy tématu a předplatného, které jste vytvořili. Tyto hodnoty použijeme v ukázkách.

> [!NOTE]
> - Tento kurz funguje s ukázkami, které můžete zkopírovat a spustit pomocí [NodeJS](https://nodejs.org/). Pokyny k vytvoření aplikace Node. js najdete v tématu [Vytvoření a nasazení aplikace Node. js na web Azure](../app-service/app-service-web-get-started-nodejs.md)nebo [cloudovou službu Node. js pomocí prostředí Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Nový balíček [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) ještě nepodporuje vytváření topcis a předplatných. Pokud je chcete programově vytvořit, použijte prosím balíček [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) .

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalace balíčku pomocí Node Package Manageru (NPM)
Chcete-li nainstalovat balíček npm pro Service Bus, otevřete příkazový řádek, který má v cestě `npm`, změňte adresář na složku, ve které chcete mít ukázky, a pak spusťte tento příkaz.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
Interakce s tématem Service Bus začíná vytvořením instance třídy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) a jejím použitím k vytvoření instance třídy [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) . Jakmile budete mít klienta tématu, můžete vytvořit odesílatele a k odesílání zpráv použít buď metodu [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) , nebo [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) .

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte soubor s názvem `send.js` a vložte do něj následující kód. Tento kód odešle 10 zpráv do tématu.

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
3. Do výše uvedeného kódu zadejte připojovací řetězec a název vašeho tématu.
4. Pak spusťte příkaz `node send.js` na příkazovém řádku, aby se tento soubor spustil. 

Blahopřejeme! Právě jste odeslali zprávy do fronty Service Bus.

Zprávy mají některé standardní vlastnosti, například `label` a `messageId`, které můžete nastavit při odesílání. Pokud chcete nastavit jakékoli vlastní vlastnosti, použijte `userProperties`, což je objekt JSON, který může uchovávat páry klíč-hodnota vašich vlastních dat.

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Počet zpráv držených v tématu není nijak omezený, ale celková velikost zpráv držených v tématu je omezena. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách najdete v tématu [Service Bus kvóty](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Přijímání zpráv z předplatného
Interakce s předplatným Service Bus začíná vytvořením instance třídy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) a jejím použitím k vytvoření instance třídy [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) . Jakmile budete mít klienta předplatného, můžete vytvořit příjemce a pro příjem zpráv použít buď metodu [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) nebo [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) .

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte soubor s názvem `recieve.js` a vložte do něj následující kód. Tento kód se pokusí získat 10 zpráv z vašeho předplatného. Skutečný počet, který obdržíte, závisí na počtu zpráv v předplatném a na latenci sítě.

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
3. Do výše uvedeného kódu zadejte připojovací řetězec a názvy vašeho tématu a předplatného.
4. Pak spusťte příkaz `node receiveMessages.js` na příkazovém řádku, aby se tento soubor spustil.

Blahopřejeme! Právě jste dostali zprávy z předplatného Service Bus.

Metoda [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) přebírá v `ReceiveMode`, což je výčet s hodnotami [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) a [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Nezapomeňte [vyrovnávat zprávy](message-transfers-locks-settlement.md#settling-receive-operations) , pokud použijete režim `PeekLock` pomocí kterékoli z `complete()`, `abandon()`, `defer()`nebo `deadletter()` metod ve zprávě.

## <a name="subscription-filters-and-actions"></a>Akce a filtry předplatného
Service Bus podporuje [filtry a akce v předplatných](topic-filters.md), což umožňuje filtrovat příchozí zprávy do předplatného a upravovat jejich vlastnosti.

Jakmile budete mít instanci `SubscriptionClient` můžete pomocí níže uvedených metod získat, přidat a odebrat pravidla v rámci předplatného a řídit tak filtry a akce.

- getrules
- addRule
- removeRule

Každé předplatné má výchozí pravidlo, které používá filtr true k povolení všech příchozích zpráv. Když přidáte nové pravidlo, nezapomeňte odebrat výchozí filtr, aby se filtr v novém pravidle fungoval. Pokud předplatné nemá žádná pravidla, nepřijde žádné zprávy.

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Další informace najdete v následujících zdrojích informací.

- [Fronty, témata a odběry](service-bus-queues-topics-subscriptions.md)
- Rezervace dalších [ukázek NodeJS pro Service Bus na GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Středisko pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/)


