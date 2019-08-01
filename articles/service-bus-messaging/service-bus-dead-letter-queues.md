---
title: Fronty nedoručených zpráv Service Bus | Microsoft Docs
description: Přehled Azure Service Bus front nedoručených zpráv
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
ms.date: 05/21/2019
ms.author: aschhab
ms.openlocfilehash: afa2e6e46579d9ce2906e2686cf40adf4b65ab2b
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516598"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Přehled Service Bus front nedoručených zpráv

Azure Service Bus fronty a odběry témat poskytují sekundární podfrontu s názvem frontu nedoručených *zpráv* (DLQ). Frontu nedoručených zpráv není nutné explicitně vytvářet a nelze ji odstranit ani jinak spravovat nezávisle na hlavní entitě.

Tento článek popisuje fronty nedoručených zpráv v Service Bus. Mnohé diskuze jsou znázorněné ve vzorové frontě nedoručených [zpráv](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) na GitHubu.
 
## <a name="the-dead-letter-queue"></a>Fronta nedoručených zpráv

Účelem fronty nedoručených zpráv je uchovávat zprávy, které nemohou být doručeny žádnému příjemci nebo zprávy, které nebylo možné zpracovat. Zprávy je pak možné odebrat z DLQ a zkontrolovat. Aplikace může pomocí operátoru, opravit problémy a znovu odeslat zprávu, zaznamenat fakt, že došlo k chybě, a provést nápravné opatření. 

Z hlediska rozhraní API a protokolu se DLQ většinou podobá jakékoli jiné frontě, s tím rozdílem, že zprávy je možné odeslat jenom prostřednictvím operace nedoručené pošty nadřazené entity. Kromě toho se nepozoruje doba do provozu a nemůžete nedoručenou zprávu z DLQ. Fronta nedoručených zpráv plně podporuje operace čtení a transakcí s náhledem.

Všimněte si, že není k dispozici žádné automatické vyčištění DLQ. Zprávy zůstávají v DLQ, dokud je explicitně nenačtete z DLQ a voláním metody [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na zprávu s nedoručenými zprávami.

## <a name="moving-messages-to-the-dlq"></a>Přesun zpráv do DLQ

V Service Bus je několik aktivit, které způsobují, že se zprávy dostanou do DLQ z samotného stroje pro zasílání zpráv. Aplikace může také explicitně přesouvat zprávy do DLQ. 

Jak je zpráva přesunuta zprostředkovatelem, jsou do zprávy přidány dvě vlastnosti, protože zprostředkovatel volá svou interní verzi metody nedoručených [zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) ve zprávě: `DeadLetterReason` a `DeadLetterErrorDescription`.

Aplikace mohou definovat vlastní kódy pro `DeadLetterReason` vlastnost, ale systém nastaví následující hodnoty.

| Podmínka | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Vždy |HeaderSizeExceeded |Kvóta velikosti pro tento datový proud byla překročena. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing a SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |jímka. Zpráva |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Zprávě vypršela platnost a zařadila se do fronty nedoručených zpráv. |
| SubscriptionDescription.RequiresSession |ID relace je null. |Entita povolená relací nepodporuje zprávy, jejichž identifikátor relace má hodnotu null. |
| ! fronta nedoručených zpráv | MaxTransferHopCountExceeded | Maximální počet povolených směrování při předávání mezi frontami. Hodnota je nastavená na 4. |
| Explicitní nedoručená závorka aplikace |Určeno aplikací |Určeno aplikací |

## <a name="exceeding-maxdeliverycount"></a>Překročení MaxDeliveryCount

Fronty a odběry mají každý z nich vlastnost [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) a [SubscriptionDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) ; Výchozí hodnota je 10. Pokaždé, když se zpráva doručí za zámek ([ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), ale buď se explicitně zrušila, nebo vypršela platnost zámku, zpráva [BrokeredMessage. DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) se zvýší. Pokud [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) překračuje [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), zpráva se přesune na DLQ `MaxDeliveryCountExceeded` a určí kód důvodu.

Toto chování nelze zakázat, ale můžete nastavit [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) na velmi velké číslo.

## <a name="exceeding-timetolive"></a>Překročení TimeToLive

Pokud je vlastnost [QueueDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) nebo [SubscriptionDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) nastavená na **hodnotu true** (výchozí hodnota je **false**), všechny zprávy o vypršení platnosti jsou přesunuta do DLQ s určením `TTLExpiredException` kódu důvodu.

Všimněte si, že zprávy s vypršenou platností jsou vymazány a přesunuty do DLQ, pokud se alespoň jeden aktivní přijímač vybírá z hlavní fronty nebo předplatného. Toto chování je záměrné.

## <a name="errors-while-processing-subscription-rules"></a>Chyby při zpracování pravidel předplatného

Pokud je pro odběr povolena vlastnost [SubscriptionDescription. EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) , všechny chyby, ke kterým dojde při spuštění pravidla filtru SQL předplatného, jsou zachyceny v DLQ spolu s problematickým Zpráva.

## <a name="application-level-dead-lettering"></a>Nedoručená písmena na úrovni aplikace

Kromě funkcí nedoručených zpráv poskytovaných systémem může aplikace DLQ použít k explicitnímu zamítnutí nepřijatelných zpráv. To může zahrnovat zprávy, které se nedají správně zpracovat z důvodu jakéhokoli druhu problémů se systémem, zpráv, které obsahují poškozené datové části nebo zprávy, které se při použití některého schématu zabezpečení na úrovni zpráv nedaří ověřování.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Nedoručené písmeno ve scénářích ForwardTo nebo SendVia

Zprávy se odešlou do fronty nedoručených zpráv přenosu za následujících podmínek:

- Zpráva projde více než 4 frontami nebo tématy zřetězenými [dohromady](service-bus-auto-forwarding.md).
- Cílová fronta nebo téma jsou zakázané nebo odstraněné.
- Cílová fronta nebo téma překračuje maximální velikost entity.

Chcete-li načíst tyto nedoručené zprávy, můžete vytvořit příjemce pomocí metody [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) Utility.

## <a name="example"></a>Příklad

Následující fragment kódu vytvoří příjemce zprávy. Ve smyčce Receive pro hlavní front kód načte zprávu s [příjmem (TimeSpan. Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), který požádá zprostředkovatele, aby okamžitě vrátil libovolnou zprávu, která je snadno dostupná, nebo se vrátí bez výsledku. Pokud kód obdrží zprávu, okamžitě ho opustí, čímž se `DeliveryCount`zvýší. Jakmile systém přesune zprávu do DLQ, hlavní fronta je prázdná a smyčka se ukončí, protože [metody ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) vrátí **hodnotu null**.

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

Pokud používáte sadu .NET SDK, můžete získat cestu k frontě nedoručených zpráv pomocí metody SubscriptionClient. FormatDeadLetterPath (). Tato metoda přebírá název a název předplatného tématu a přípona s **/$DeadLetterQueue**.


## <a name="next-steps"></a>Další postup

Další informace o frontách Service Bus najdete v následujících článcích:

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Ve srovnání s frontami Azure a frontami Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

