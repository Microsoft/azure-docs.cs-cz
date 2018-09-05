---
title: Přehled zpracování transakcí v Azure Service Bus | Dokumentace Microsoftu
description: Přehled služby Azure Service Bus atomické transakce a odeslat prostřednictvím
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: spelluru
ms.openlocfilehash: bc0e0bfb8d4dc8637c191785a98f5d15e086cbf5
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696427"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Přehled služby Service Bus zpracování transakcí

Tento článek popisuje možnosti transakce služby Microsoft Azure Service Bus. Velká část diskuse je znázorněn ve [atomické transakce s ukázkou služby Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions). Tento článek je omezená na základní informace o zpracování transakcí a *odeslat prostřednictvím* funkcí ve službě Service Bus, zatímco ukázka atomické transakce je širší a složitější v oboru.

## <a name="transactions-in-service-bus"></a>Transakce ve službě Service Bus

A [ *transakce* ](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions#what-are-transactions) seskupuje dva nebo více operací do *provádění oboru*. Podle povahy musíte tyto transakce zajistit, že všechny operace, které patří k dané skupině operací úspěšné, nebo selže společně. V tomto ohledu transakce fungují jako jednu jednotku, která se často označuje jako *atomicitu*. 

Service Bus je zprostředkovatel transakčních zpráv a zajišťuje transakční integrity pro všechny interní operace s jeho úložiště zpráv. Všechny přenosy zpráv v rámci služby Service Bus, jako je například přesun zpráv [fronty nedoručených zpráv](service-bus-dead-letter-queues.md) nebo [automatické přesměrování](service-bus-auto-forwarding.md) zpráv mezi entitami, které jsou transakční. V důsledku toho pokud služby Service Bus přijme zprávu, je již uloženy a popisek s pořadovým číslem. Od té chvíle případné přenosy zpráv v rámci služby Service Bus jsou koordinovat operace napříč entitami a ani povede ke ztrátě (úspěšný zdroj a cíl selže) nebo k duplikaci (zdroj selže a úspěšné cíl) zprávy.

Service Bus podporuje operace seskupení u jedné entity zasílání zpráv (fronty, tématu, odběru) v rámci oboru transakce. Například odešlete několik zpráv pro jednu frontu z v rámci oboru transakcí a zprávy pouze budou potvrzeny do protokolu do fronty po úspěšném dokončení transakce.

## <a name="operations-within-a-transaction-scope"></a>Operace v rámci oboru transakcí

Operace, které lze provést v rámci oboru transakce jsou následující:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: odeslání, SendAsync SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: dokončení CompleteAsync, zrušení, AbandonAsync, nedoručených zpráv, DeadletterAsync, odložit, DeferAsync, RenewLock, RenewLockAsync 

Zobrazí operace nejsou zahrnuty, protože se předpokládá, že aplikace získá zprávy pomocí [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) smyčka příjmu režimu do některých nebo s [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) zpětné volání, a teprve potom se otevře oboru transakce pro zpracování zprávy.

Dispozice zprávy (dokončeno, zrušení, dead-letter odložit) potom se provede v rámci oboru a v závislosti na celkový výsledek transakce.

## <a name="transfers-and-send-via"></a>Převody a "odeslat prostřednictvím"

Umožňuje transakční předání dat z fronty procesoru a potom do jiné fronty služby Service Bus podporuje *přenosy*. V rámci operace přenosu odesílatel nejdříve odešle zprávu *fronty přenosu*, a fronty přenosu okamžitě přesune zprávu do fronty požadovaného cíle pomocí stejné implementaci robustní přenos, který auto-forward Funkce spoléhá na. Zpráva se nikdy zavazuje fronty přenosu protokolu tak, že se zobrazí uživatelům fronty přenosu.

Výkon této transakční funkce pocítíte, když samotné fronty přenosu je zdrojem odesílatele zprávy o zadávání. Jinými slovy, Service Bus se můžou přenášet zprávy do cílové fronty "přes" frontě přenosů při provádění úplná (nebo odložit, nebo nedoručené zprávy) operace na vstupní zprávy v rámci jedné atomické operace. 

### <a name="see-it-in-code"></a>Prohlédněte si portál v kódu

Chcete-li nastavit tyto převody, můžete vytvořit odesílatele zprávy, který cílí na cílové fronty prostřednictvím fronty přenosu. Máte také příjemce, která si přetáhne zprávy z této fronty stejné. Příklad:

```csharp
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Jednoduché transakce pak používá tyto prvky, jako v následujícím příkladu:

```csharp
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Další postup

Naleznete v následujících článcích pro další informace o fronty služby Service Bus:

* [Jak používat fronty Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Řetězení entit služby Service Bus s automatickým přeposíláním](service-bus-auto-forwarding.md)
* [Auto-forward vzorku](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomické transakce s ukázkou služby Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Fronty Azure a služby Service Bus fronty porovnání](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


