---
title: Přehled zpracování transakcí v Azure Service Bus | Microsoft Docs
description: Přehled Azure Service Bus jednotlivé transakce a odesílání prostřednictvím
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: d2e3fc7c59e0b57e77d2239ff73368f96426ef39
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
ms.locfileid: "29558937"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Přehled služby Service Bus zpracování transakcí

Tento článek popisuje možnosti transakce Microsoft Azure Service Bus. Hodně diskuse je zobrazená ve [jednotlivé transakce s ukázkou Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions). Tento článek je omezený na přehled zpracování transakcí a *odesílání prostřednictvím* funkce v Service Bus, zatímco ukázka jednotlivé transakce je širší a složitější v oboru.

## <a name="transactions-in-service-bus"></a>Transakce v Service Bus

A [ *transakce* ](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions#what-are-transactions) seskupí dva nebo více operací společně do *spuštění rozsah*. Vzhledem k povaze tuto transakci Ujistěte se, že všechny operace, které patří do dané skupiny operací úspěšné, nebo selže společně. V tomto ohledu transakce fungovat jako jednu jednotku, která se často označuje jako *nedělitelnost*. 

Service Bus je zprostředkovatele transakčních zpráv a zajistíte integritu transakcí pro všechny interní operace před jeho úložiště zpráv. Všechny přenosy zpráv v rámci služby Service Bus, například přesun zpráv [frontu nedoručených zpráv](service-bus-dead-letter-queues.md) nebo [automatické přesměrování](service-bus-auto-forwarding.md) zpráv mezi entitami, jsou transakcí. Jako takový Pokud Service Bus přijme zprávu, již bylo uložené a s názvem bez přípony s pořadovým číslem. Od toho všechny přenosy zprávy v rámci služby Service Bus jsou koordinované operace napříč entity a ani jeden z nich povede ke ztrátě (úspěšné zdroj a cíl selže) nebo duplikace (selže zdroj a cíl úspěšné) zprávy.

Service Bus podporuje seskupení operace u jedné entity přenosu zpráv (fronty, tématu, odběru) v rámci oboru transakce. Například mohou zasílat několik zprávy do jedné frontu z v oboru transakce a zprávy pouze budou potvrzeny do fronty protokolu při úspěšném dokončení transakce.

## <a name="operations-within-a-transaction-scope"></a>Operace v rámci oboru transakce

Operace, které lze provést v oboru transakce jsou následující:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: odeslat, SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: dokončení CompleteAsync, Abandon, AbandonAsync, nedoručených zpráv, DeadletterAsync, odložení, DeferAsync, RenewLock, RenewLockAsync 

Zobrazí operace nejsou zahrnuty, protože se předpokládá, že aplikace získá zprávy pomocí [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) režimu uvnitř některé přijímat smyčky nebo s [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) zpětného volání, a Otevře se pak oboru transakce pro zpracování zprávy.

Poté dojde k dispozice zprávy (dokončení odložení abandon nedoručených zpráv) v rámci oboru a v závislosti na celkový výsledek transakce.

## <a name="transfers-and-send-via"></a>Přenosy a "odesílání prostřednictvím"

Pokud chcete povolit transakční předání dat z fronty pro procesor a pak do jiné fronty, Service Bus podporuje *přenosy*. V rámci operace přenosu odesílatel nejprve odešle zprávu *fronty přenosu*, a fronty přenosu okamžitě přesune zprávu do určené cílové fronty pomocí stejné implementace robustní přenos, předat dál automaticky funkce využívá. Zpráva se nikdy zavazuje fronty přenosu protokolu tak, že se zobrazí u spotřebitelé fronty přenosu.

Napájení tuto funkci transakční vyvstává zřejmá, když se fronta přenosu samotného zdroji vstupní zpráv odesílatele. Jinými slovy, Service Bus můžete přenést do cílové fronty "přes" fronty přenosu při provádění úplná (nebo odložení, nebo nedoručených zpráv) operaci na vstupní zprávu, všechny v rámci jedné atomické operace. 

### <a name="see-it-in-code"></a>Zobrazení v kódu

Chcete-li nastavit tyto přenosy, vytvořte odesílatele zprávy, který cílí cílové fronty prostřednictvím fronty přenosu. Máte také příjemce, který žádá o zprávy z této fronty stejné. Příklad:

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

Najdete v následujících článcích pro další informace o fronty Service Bus:

* [Jak používat fronty Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Řetězení entit služby Service Bus s automatické přesměrování](service-bus-auto-forwarding.md)
* [Ukázka automatické vpřed](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Jednotlivé transakce s ukázkou Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Azure Service Bus a fronty fronty porovnání](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


