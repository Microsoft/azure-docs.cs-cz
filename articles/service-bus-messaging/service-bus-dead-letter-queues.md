---
title: Fronty nedoručených zpráv služby Service Bus | Microsoft Docs
description: Přehled fronty nedoručených zpráv Azure Service Bus
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: a82d70e7bf776bf470d14e7f061774ccbb136316
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28925658"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Přehled fronty nedoručených zpráv služby Service Bus

Fronty Azure Service Bus a odběry tématu poskytují sekundární dílčí fronty, názvem *frontu nedoručených zpráv* (DLQ se). Frontu nedoručených zpráv nemusí být explicitně vytvořen a nelze odstranit nebo jinak spravované nezávislé hlavní entity.

Tento článek popisuje fronty nedoručených zpráv ve sběrnici Service Bus. Hodně diskuse je zobrazená ve [Ukázka fronty nedoručených zpráv](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) na Githubu.
 
## <a name="the-dead-letter-queue"></a>Frontu nedoručených zpráv

Účelem frontu nedoručených zpráv je pro uložení zpráv, které nelze doručit do libovolné receiver nebo zprávy, které nebylo možné zpracovat. Zprávy můžete pak odebrán z DLQ se a prověřovány. Aplikace může pomocí operátoru opravit problémy a znovu odeslat zprávu protokolu skutečnost, že došlo k chybě a proveďte opravné akce. 

Z rozhraní API a protokol perspektivy DLQ se je ve většině případů podobně jako jakékoli jiné frontě s tím rozdílem, že zprávy lze odeslat pouze prostřednictvím nedoručených zpráv operace nadřazená entita. Kromě toho není dodržen time to live, a nemůžete nedoručených zpráv ze DLQ se. Frontu nedoručených zpráv plně podporuje funkce Náhled zámku doručení a transakční operace.

Všimněte si, že žádné automatické čištění DLQ se. Zprávy zůstávají v DLQ se, dokud je explicitně načíst z DLQ se a volání [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na nedoručených zpráv.

## <a name="moving-messages-to-the-dlq"></a>Přesunutí zprávy a pokuste se DLQ se

Existuje několik aktivit v Service Bus, které způsobí zprávy a pokuste se získat nabídnutých do DLQ se z v rámci samotné modul zasílání zpráv. Aplikace můžete také explicitně přesunout zprávy DLQ se. 

Získá během pohybu zprostředkovatelem zprávy, dvě vlastnosti jsou přidány do zprávy jako zprostředkovatel volá jeho vnitřní verzi [nedoručených zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) na zprávu: `DeadLetterReason` a `DeadLetterErrorDescription`.

Aplikace můžete definovat vlastní kódy pro `DeadLetterReason` vlastnost, ale systém nastaví následující hodnoty.

| Podmínka | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Vždy |HeaderSizeExceeded |Byla překročena kvóta velikosti pro tento datový proud. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing a SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |došlo k výjimce. Zpráva |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Zpráva vypršela platnost a byl mrtvých lettered. |
| SubscriptionDescription.RequiresSession |Id relace má hodnotu null. |Relace povoleno entity neumožňuje zprávu, jejíž identifikátor relace má hodnotu null. |
| ! fronty nedoručených zpráv |MaxTransferHopCountExceeded |Null |
| Aplikace explicitní mrtvých písmem. |Zadaná aplikace |Zadaná aplikace |

## <a name="exceeding-maxdeliverycount"></a>Překročení MaxDeliveryCount

Fronty a odběry mají [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) a [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) vlastnost; Výchozí hodnota je 10. Vždy, když byla doručena zpráva pod zámek ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), ale byl buď explicitně opuštění nebo zámek vypršela platnost, zprávy [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) je zvýší. Když [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) překračuje [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), zpráva bude přesunuta do DLQ se, určení `MaxDeliveryCountExceeded` kód důvodu.

Toto chování se nedá zakázat, ale můžete nastavit [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) pro velmi velký počet.

## <a name="exceeding-timetolive"></a>Exceeding TimeToLive

Když [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) nebo [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnMessageExpiration) je nastavena na **true** (výchozí hodnota je **false**), kterým vyprší platnost všechny zprávy přesunou do DLQ se, určení `TTLExpiredException` kód důvodu.

Upozorňujeme, že zprávy s vypršenou platností pouze vyprázdní a přesunuta do DLQ se, když je alespoň jeden příjemce active stahování z hlavní fronty nebo předplatného; Toto chování je záměrné.

## <a name="errors-while-processing-subscription-rules"></a>Při zpracování pravidel odběru došlo k chybám

Když [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnFilterEvaluationExceptions) vlastnost je povolená pro předplatné, všechny chyby, ke kterým dojde během předplatného SQL filtru pravidlo provádí zachyceny v DLQ se podél problematické zpráva.

## <a name="application-level-dead-lettering"></a>Zpráv lettering úrovni aplikace

Kromě zpráv lettering funkce poskytované systémem aplikace používat DLQ se tak, aby explicitně zamítal nepřijatelné zprávy. To může zahrnovat zprávy, které nelze správně zpracovat z důvodu jakéhokoli druhu systémový problém, zprávy, které mají poškozených datových částí nebo zprávy, které se ověření nezdaří, pokud se používá schéma některé zprávy úroveň zabezpečení.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Zpráv lettering ve scénářích ForwardTo nebo SendVia

Zprávy o budou odesílány do fronty nedoručených zpráv přenosu za následujících podmínek:

- Zpráva projdou více než 3 fronty a témata, která jsou [zřetězen dohromady](service-bus-auto-forwarding.md).
- Cílová fronta nebo téma je deaktivované nebo odstraněné.
- Cílová fronta nebo téma překračuje velikost maximální entity.

Chcete-li získat tyto zprávy lettered zpráv, můžete vytvořit příjemce pomocí [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) metody nástrojů.

## <a name="example"></a>Příklad:

Následující fragment kódu vytvoří příjemce zprávy. Ve smyčce receive pro hlavní frontu, kód načte zprávu s [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_Receive_System_TimeSpan_), s dotazem, broker okamžitě vrátit jakékoli zprávy snadno dostupné, nebo pro návrat s žádný výsledek. Pokud kód přijme nějakou zprávu, je v okamžitě opustí ho, které se zvýší `DeliveryCount`. Jakmile systém přesune DLQ se zpráva, je hlavní fronta prázdná a ukončení smyčky jako [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_ReceiveAsync_System_TimeSpan_) vrátí **null**.

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

## <a name="next-steps"></a>Další postup

Najdete v následujících článcích pro další informace o fronty Service Bus:

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Azure Service Bus a fronty fronty porovnání](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

