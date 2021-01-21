---
title: Fronty nedoručených zpráv Service Bus | Microsoft Docs
description: Popisuje fronty nedoručených zpráv v Azure Service Bus. Service Bus fronty a odběry témat poskytují sekundární dílčí frontu, která se nazývá fronta nedoručených zpráv.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: ad62f946584071e7ce6fd55f48b5f7ee8db44a2f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630094"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Přehled Service Bus front nedoručených zpráv

Azure Service Bus fronty a odběry témat poskytují sekundární dílčí frontu nazvanou *frontu nedoručených zpráv* (DLQ). Fronta nedoručených zpráv nemusí být explicitně vytvořena a nelze ji odstranit ani jinak spravovat nezávisle na hlavní entitě.

Tento článek popisuje fronty nedoručených zpráv v Service Bus. Mnohé diskuze jsou znázorněné ve [vzorové frontě nedoručených zpráv](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) na GitHubu.
 
## <a name="the-dead-letter-queue"></a>Fronta nedoručených zpráv

Účelem fronty nedoručených zpráv je uchovávat zprávy, které nelze doručit žádnému příjemci, nebo zprávy, které nebylo možné zpracovat. Zprávy je pak možné odebrat z DLQ a zkontrolovat. Aplikace může pomocí operátoru, opravit problémy a znovu odeslat zprávu, zaznamenat fakt, že došlo k chybě, a provést nápravné opatření. 

Z hlediska rozhraní API a protokolu se DLQ většinou podobá jakékoli jiné frontě, s tím rozdílem, že zprávy je možné odeslat jenom prostřednictvím operace nedoručené pošty nadřazené entity. Kromě toho není pozor na hodnotu Time to Live a nemůžete nedoručenou zprávu z DLQ. Fronta nedoručených zpráv plně podporuje operace čtení a transakcí s náhledem.

Není k dispozici žádné automatické vyčištění DLQ. Zprávy zůstávají v DLQ, dokud je explicitně nenačtete z DLQ a voláním metody [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na zprávu s nedoručenými zprávami.

## <a name="dlq-message-count"></a>Počet zpráv DLQ
Počet zpráv ve frontě nedoručených zpráv není možné získat na úrovni tématu. Důvodem je to, že zprávy nejsou na úrovni tématu, pokud Service Bus vyvolá vnitřní chybu. Místo toho, když odesílatel pošle zprávu do tématu, zpráva se přepošle k předplatným pro toto téma během milisekund, takže se už nebude nacházet na úrovni tématu. Takže můžete zobrazit zprávy v DLQ přidružené k předplatnému pro téma. V následujícím příkladu **Service Bus Explorer** ukazuje, že v DLQ pro předplatné "test1" jsou aktuálně 62 zprávy. 

![Počet zpráv DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Počet zpráv DLQ můžete také získat pomocí příkazu rozhraní příkazového řádku Azure: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show) . 

## <a name="moving-messages-to-the-dlq"></a>Přesun zpráv do DLQ

V Service Bus je několik aktivit, které způsobují, že se zprávy dostanou do DLQ z samotného stroje pro zasílání zpráv. Aplikace může také explicitně přesouvat zprávy do DLQ. 

Jak je zpráva přesunuta zprostředkovatelem, jsou do zprávy přidány dvě vlastnosti, protože zprostředkovatel volá svou interní verzi metody [nedoručených zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) ve zprávě: `DeadLetterReason` a `DeadLetterErrorDescription` .

Aplikace mohou definovat vlastní kódy pro `DeadLetterReason` vlastnost, ale systém nastaví následující hodnoty.

| DeadLetterReason | DeadLetterErrorDescription |
| --- | --- |
|HeaderSizeExceeded |Kvóta velikosti pro tento datový proud byla překročena. |
|TTLExpiredException |Zprávě vypršela platnost a zařadila se do fronty nedoručených zpráv. Podrobnosti najdete v části [překročení TimeToLive](#exceeding-timetolive) . |
|ID relace je null. |Entita povolená relací nepodporuje zprávy, jejichž identifikátor relace má hodnotu null. |
|MaxTransferHopCountExceeded | Maximální počet povolených směrování při předávání mezi frontami. Hodnota je nastavená na 4. |
| MaxDeliveryCountExceededExceptionMessage | Zprávu nelze spotřebovat po maximálním počtu pokusů o doručení. Podrobnosti najdete v části [překročení MaxDeliveryCount](#exceeding-maxdeliverycount) . |

## <a name="exceeding-maxdeliverycount"></a>Překročení MaxDeliveryCount

Fronty a odběry mají každý z nich vlastnost [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) a [SubscriptionDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) ; Výchozí hodnota je 10. Pokaždé, když se zpráva doručí za zámek ([ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), ale buď se explicitně zrušila, nebo vypršela platnost zámku, zpráva [BrokeredMessage. DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) se zvýší. Pokud [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) překračuje [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), zpráva se přesune na DLQ a určí `MaxDeliveryCountExceeded` kód důvodu.

Toto chování nelze zakázat, ale můžete nastavit [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) na velké číslo.

## <a name="exceeding-timetolive"></a>Překročení TimeToLive

Pokud je vlastnost [QueueDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) nebo [SubscriptionDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) nastavena na **hodnotu true** (výchozí hodnota je **false**), všechny zprávy o vypršení platnosti se přesunou do DLQ a určí  `TTLExpiredException` kód důvodu.

Zprávy s vypršenou platností jsou vymazány a přesunuty do DLQ, pokud je alespoň jeden aktivní příjem z hlavní fronty nebo předplatného a [odložené zprávy](./message-deferral.md) nebudou vymazány a přesunuty do fronty nedoručených zpráv po vypršení platnosti. Tyto chování jsou záměrné.

## <a name="errors-while-processing-subscription-rules"></a>Chyby při zpracování pravidel předplatného

Je-li pro odběr povolena vlastnost [SubscriptionDescription. EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) , všechny chyby, ke kterým dojde při spuštění pravidla filtru SQL předplatného, jsou zachyceny v DLQ spolu s problematickou zprávou. Tuto možnost nepoužívejte v produkčním prostředí, ve kterém ne všechny typy zpráv mají předplatitele.

## <a name="application-level-dead-lettering"></a>Nedoručená písmena na úrovni aplikace

Kromě funkcí nedoručených zpráv poskytovaných systémem může aplikace DLQ použít k explicitnímu zamítnutí nepřijatelných zpráv. Můžou zahrnovat zprávy, které se nedají správně zpracovat kvůli jakémukoli druhu problémů se systémem, zprávám, které obsahují poškozené datové části nebo zprávy, které selžou ověřování při použití určitého schématu zabezpečení na úrovni zprávy.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Nedoručené písmeno ve scénářích ForwardTo nebo SendVia

Zprávy se odešlou do fronty nedoručených zpráv přenosu za následujících podmínek:

- Zpráva projde více než čtyřmi frontami nebo tématy [zřetězenými dohromady](service-bus-auto-forwarding.md).
- Cílová fronta nebo téma jsou zakázané nebo odstraněné.
- Cílová fronta nebo téma překračuje maximální velikost entity.

Chcete-li načíst tyto nedoručené zprávy, můžete vytvořit příjemce pomocí metody [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) Utility.

## <a name="example"></a>Příklad

Následující fragment kódu vytvoří příjemce zprávy. Ve smyčce Receive pro hlavní front kód načte zprávu s [příjmem (TimeSpan. Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), který požádá zprostředkovatele, aby okamžitě vrátil libovolnou zprávu, která je snadno dostupná, nebo se vrátí bez výsledku. Pokud kód obdrží zprávu, okamžitě ho opustí, čímž se zvýší  `DeliveryCount` . Jakmile systém přesune zprávu do DLQ, hlavní fronta je prázdná a smyčka se ukončí, protože [metody ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) vrátí **hodnotu null**.

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


## <a name="next-steps"></a>Další kroky

Další informace o frontách Service Bus najdete v následujících článcích:

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Ve srovnání s frontami Azure a frontami Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

