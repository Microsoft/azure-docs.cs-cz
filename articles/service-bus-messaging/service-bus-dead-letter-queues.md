---
title: Fronty nedoručených zpráv service bus | Dokumenty společnosti Microsoft
description: Popisuje fronty nedoručených zpráv v Azure Service Bus. Fronty service bus a předplatná tématu poskytují sekundární podfrontu, která se nazývá fronta nedoručených zpráv.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: 9c1a0cb92fbaf98d25799ffb5a85e666e7c05f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158893"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Přehled front nedoručených zpráv service bus

Fronty Azure Service Bus a předplatná tématu poskytují sekundární podfrontu, která se nazývá *fronta nedoručených zpráv* (DLQ). Frontu nedoručených zpráv není nutné explicitně vytvořit a nelze ji odstranit ani jinak spravovat nezávisle na hlavní entitě.

Tento článek popisuje fronty nedoručených zpráv v service bus. Velká část diskuse je ilustrována [ukázkou front nedoručených zpráv](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) na GitHubu.
 
## <a name="the-dead-letter-queue"></a>Fronta nedoručených zpráv

Účelem fronty nedoručených zpráv je uchovat zprávy, které nelze doručit žádnému příjemci, nebo zprávy, které nelze zpracovat. Zprávy pak mohou být odebrány z DLQ a kontrolovány. Aplikace může pomocí operátoru opravit problémy a znovu odeslat zprávu, protokolovat skutečnost, že došlo k chybě, a provést nápravná opatření. 

Z hlediska rozhraní API a protokolu DLQ je většinou podobný jakékoli jiné fronty, s tím rozdílem, že zprávy lze odeslat pouze prostřednictvím operace nedoručených zpráv nadřazené entity. Kromě toho time-to-live není dodržena a nelze mrtvé-dopis zprávu zprávu z DLQ. Fronta nedoručených zpráv plně podporuje doručování náhledu a transakční operace.

Neexistuje žádné automatické vyčištění DLQ. Zprávy zůstávají v DLQ, dokud explicitně načíst z DLQ a volání [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na nedoručené zprávy.

## <a name="dlq-message-count"></a>Počet zpráv DLQ
Není možné získat počet zpráv ve frontě nedoručených zpráv na úrovni tématu. Je to proto, že zprávy nejsou sedět na úrovni tématu, pokud Service Bus vyvolá vnitřní chybu. Místo toho když odesílatel odešle zprávu na téma, zpráva je předána odběry pro téma v milisekundách a proto již nesídlí na úrovni tématu. Takže můžete vidět zprávy v DLQ přidružené k předplatnému pro toto téma. V následujícím příkladu **Service Bus Explorer** ukazuje, že jsou 62 zprávy aktuálně v DLQ pro odběr "test1". 

![Počet zpráv DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Počet zpráv DLQ můžete také získat pomocí příkazu [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription?view=azure-cli-latest#az-servicebus-topic-subscription-show)Azure CLI: . 

## <a name="moving-messages-to-the-dlq"></a>Přesouvání zpráv do DLQ

Existuje několik aktivit v Service Bus, které způsobí, že zprávy získat tlačil do DLQ z v rámci samotného modulu zasílání zpráv. Aplikace může také explicitně přesunout zprávy do DLQ. 

Jako zpráva se přesune zprostředkovatelem, dvě vlastnosti jsou přidány do zprávy jako zprostředkovatel volá jeho `DeadLetterReason` `DeadLetterErrorDescription`interní verzi [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) metody na zprávu: a .

Aplikace mohou definovat své `DeadLetterReason` vlastní kódy pro vlastnost, ale systém nastaví následující hodnoty.

| Podmínka | DeadLetterReason | Popis chyby v programu DeadLetterError |
| --- | --- | --- |
| Vždy |HlavičkaByla překročena. |Kvóta velikosti pro tento datový proud byla překročena. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing a SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |Výjimka. GetType(). Jméno |Výjimka. Zprávu |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Zprávě vypršela platnost a zařadila se do fronty nedoručených zpráv. |
| SubscriptionDescription.RequiresSession |ID relace je null. |Entita povolená relací nepodporuje zprávy, jejichž identifikátor relace má hodnotu null. |
| !fronta nedoručených zpráv | MaxTransferHopCountbyl překročen. | Maximální počet povolených směrování při předávání mezi frontami. Hodnota je nastavena na 4. |
| Explicitní nedoručené písmo aplikace |Určeno aplikací |Určeno aplikací |

## <a name="exceeding-maxdeliverycount"></a>Překročení MaxDeliveryCount

Fronty a odběry mají vlastnost [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) a [SubscriptionDescription.MaxDeliveryCount;](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) výchozí hodnota je 10. Vždy, když zpráva byla doručena pod zámkem ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), ale byla explicitně opuštěna nebo vypršela platnost zámku, zpráva [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) je přírůstek. Když [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) překročí [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), zpráva je přesunuta do `MaxDeliveryCountExceeded` DLQ, zadání kódu důvodu.

Toto chování nelze zakázat, ale můžete nastavit [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) na velké číslo.

## <a name="exceeding-timetolive"></a>Překročení TimeToLive

Když [queueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) nebo [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) vlastnost je nastavena na **hodnotu true** (výchozí hodnota je **false**), všechny zprávy s končící platností jsou přesunuty do DLQ, zadání `TTLExpiredException` maty kód.

Zprávy s ukončenou platností jsou vymazány a přesunuty do dlq pouze v případě, že je alespoň jeden aktivní příjemce vytažení z hlavní fronty nebo předplatného; toto chování je záměrné.

## <a name="errors-while-processing-subscription-rules"></a>Chyby při zpracování pravidel předplatného

Při [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) vlastnost je povolena pro odběr, všechny chyby, ke kterým dojde při spuštění pravidla filtru SQL předplatného jsou zachyceny v DLQ spolu s problematickou zprávu.

## <a name="application-level-dead-lettering"></a>Nedoručené písmo na úrovni aplikace

Kromě funkcí nedoručených zpráv poskytovaných systémem mohou aplikace použít dlq explicitně odmítnout nepřijatelné zprávy. Mohou obsahovat zprávy, které nelze správně zpracovat z důvodu jakéhokoli systémového problému, zprávy, které obsahují poškozené datové části, nebo zprávy, které při použití některých systémů zabezpečení na úrovni zpráv nepodaří ověření.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Nedoručené písmo ve scénářích ForwardTo nebo SendVia

Zprávy budou odeslány do fronty nedoručených zpráv přenosu za následujících podmínek:

- Zpráva prochází více než čtyřmi frontami nebo tématy, které jsou [zřetězené dohromady](service-bus-auto-forwarding.md).
- Cílová fronta nebo téma je zakázáno nebo odstraněno.
- Cílová fronta nebo téma překračuje maximální velikost entity.

Chcete-li načíst tyto zprávy s nedoručeným písmem, můžete vytvořit příjemce pomocí metody nástroje [FormatTransferDeadletterPath.](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath)

## <a name="example"></a>Příklad

Následující fragment kódu vytvoří příjemce zprávy. Ve smyčce příjmu pro hlavní fronty kód načte zprávu s [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), který požádá zprostředkovatele okamžitě vrátit všechny zprávy snadno dostupné nebo vrátit bez výsledku. Pokud kód obdrží zprávu, okamžitě ji opustí, což se `DeliveryCount`zpřísňuje . Jakmile systém přesune zprávu do DLQ, hlavní fronta je prázdná a smyčka ukončí, jako [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) vrátí **null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>Cesta k frontě nedoručených zpráv
K frontě nedoručených zpráv můžete přistupovat pomocí následující syntaxe:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Pokud používáte sdk .NET, můžete získat cestu do fronty nedoručených zpráv pomocí metody SubscriptionClient.FormatDeadLetterPath(). Tato metoda přebírá název tématu/název předplatného a přípony s **/$DeadLetterQueue**.


## <a name="next-steps"></a>Další kroky

Další informace o frontách služby Service Bus naleznete v následujících článcích:

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Porovnání front azure a sběrnice Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

