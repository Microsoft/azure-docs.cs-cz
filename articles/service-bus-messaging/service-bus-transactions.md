---
title: Přehled zpracování transakcí v Azure Service Bus
description: Tento článek poskytuje přehled zpracování transakcí a odesílání prostřednictvím funkce v Azure Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 22744ecbced40b3195f4d047227b1e2a37228102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260901"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Přehled zpracování transakcí sběrnice Service Bus

Tento článek popisuje možnosti transakcí služby Microsoft Azure Service Bus. Velká část diskuse je ilustrována [amqp transakce s service bus vzorku](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Tento článek je omezen na přehled zpracování transakcí a *odesílání prostřednictvím* funkce v Service Bus, zatímco ukázka atomic transactions je širší a složitější v oboru.

## <a name="transactions-in-service-bus"></a>Transakce v sběrnici Service Bus

Transakce *transaction* seskupí dvě nebo více operací do *oboru spuštění*. Taková transakce musí ze své povahy zajistit, aby všechny operace patřící do dané skupiny operací buď uspěly, nebo společně neuspěly. V tomto ohledu transakce fungují jako jedna jednotka, která je často označována jako *nedělitost*.

Service Bus je zprostředkovatel transakční zprávy a zajišťuje transakční integritu pro všechny interní operace proti jeho úložiště zpráv. Všechny přenosy zpráv uvnitř service bus, jako je například přesunutí zpráv do [fronty nedoručených zpráv](service-bus-dead-letter-queues.md) nebo [automatické předávání](service-bus-auto-forwarding.md) zpráv mezi entitami, jsou transakční. Jako takové, pokud Service Bus přijme zprávu, již byla uložena a označena pořadovým číslem. Od té doby všechny přenosy zpráv v rámci service bus jsou koordinované operace mezi entitami a nepovede ke ztrátě (zdroj úspěšné a cíl selže) nebo duplikace (zdroj selže a cíl uspěje) zprávy.

Service Bus podporuje operace seskupení u jedné entity zasílání zpráv (fronty, tématu, odběru) v rámci oboru transakce. Můžete například odeslat několik zpráv do jedné fronty z oboru transakce a zprávy budou potvrzeny pouze do protokolu fronty po úspěšném dokončení transakce.

## <a name="operations-within-a-transaction-scope"></a>Operace v rámci rozsahu transakce

Operace, které lze provést v rámci oboru transakce jsou následující:

* ** [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: Send, SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync 

Operace příjmu nejsou zahrnuty, protože se předpokládá, že aplikace získává zprávy pomocí [receiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) režimu, uvnitř některé smyčky příjmu nebo s [onMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) zpětné volání a teprve potom otevře rozsah transakce pro zpracování zprávy.

Dispozice zprávy (úplné, opuštění, nedoručené, odložení) pak dojde v rámci a závisí na celkovém výsledku transakce.

## <a name="transfers-and-send-via"></a>Převody a "odeslat přes"

Chcete-li povolit transakční předání dat z fronty do procesoru a potom do jiné fronty, service bus podporuje *přenosy*. V operaci přenosu odesílatel nejprve odešle zprávu do *fronty přenosu*a fronta přenosu okamžitě přesune zprávu do cílové fronty s použitím stejné robustní implementace přenosu, na kterou závisí funkce automatického předávání. Zpráva je nikdy potvrzena do protokolu fronty přenosu tak, aby byla viditelná pro spotřebitele fronty přenosu.

Síla této transakční schopnosti se projeví, když je zdrojem vstupních zpráv odesílatele samotná fronta přenosu. Jinými slovy Service Bus můžete přenést zprávu do cílové fronty "přes" fronty přenosu při provádění dokončení (nebo odložit, nebo nedoručené) operace na vstupní zprávy, vše v jedné atomické operace. 

### <a name="see-it-in-code"></a>Zobrazit v kódu

Chcete-li nastavit takové přenosy, vytvořte odesílatele zprávy, který cílí na cílovou frontu prostřednictvím fronty přenosu. Máte také příjemce, který vytahuje zprávy ze stejné fronty. Například:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Jednoduchá transakce pak používá tyto prvky, jako v následujícím příkladu. Chcete-li odkazovat na úplný příklad, podívejte se [na zdrojový kód na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="next-steps"></a>Další kroky

Další informace o frontách služby Service Bus naleznete v následujících článcích:

* [Použití front služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Řetězení entit service bus s automatickým předáváním](service-bus-auto-forwarding.md)
* [Ukázka automatického přeposílání](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomové transakce se ukázkou sběrnice Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Porovnání front azure a sběrnice Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


