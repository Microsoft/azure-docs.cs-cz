---
title: Zprávy předběžného načtení Azure Service Bus | Microsoft Docs
description: Vylepšete výkon pomocí předběžného načítání Azure Service Bus zpráv. Zprávy jsou snadno dostupné pro místní načtení předtím, než je aplikace vyžádá.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 05e23b0590f0c04171efda8fb561b4c2664ed096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85341058"
---
# <a name="prefetch-azure-service-bus-messages"></a>Předběžné načtení Azure Service Busch zpráv

Pokud je v některém z oficiálních Service Bus klientů povoleno *předběžné načtení* , příjemce v tichém režimu získá další zprávy, až do limitu [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) , nad rámec toho, co aplikace poprvé požádala.

Jedno počáteční [přijetí](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) nebo volání [metody ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) proto Získá zprávu pro okamžitou spotřebu, která se vrátí hned jako dostupná. Klient pak získá další zprávy na pozadí, aby vyplnila vyrovnávací paměť předběžného načtení.

## <a name="enable-prefetch"></a>Povolit předběžné načtení

Pomocí rozhraní .NET povolíte funkci předběžného načtení nastavením vlastnosti [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) třídy **MessageReceiver**, **QueueClient** nebo **SubscriptionClient** na číslo větší než nula. Nastavením hodnoty na hodnotu nula dojde k vypnutí předběžného načtení.

Toto nastavení můžete snadno přidat na straně příjmu nastavení [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) nebo [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) Samples, abyste viděli efekt v těchto kontextech.

I když jsou zprávy k dispozici ve vyrovnávací paměti předběžného načtení, všechna následná volání metody ReceiveAsync se okamžitě **dostanou** /  z vyrovnávací paměti a vyrovnávací paměť se na pozadí doplní, jakmile bude k dispozici místo. Pokud nejsou k dispozici žádné zprávy pro doručení, operace Receive vyprázdní vyrovnávací paměť a pak počká nebo zablokuje podle očekávání.

Předběžné načtení zároveň funguje stejným způsobem s rozhraními API- [Message](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) a [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) .

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Pokud je to rychlejší, proč není předběžné, že se jedná o výchozí možnost?

Předběžné načtení urychluje tok zpráv tím, že má zprávu, která je snadno dostupná pro místní načtení, když a před tím, než aplikace požádá o jeden. Tento nárůst propustnosti je výsledkem obchodu, který musí autor aplikace provést explicitně:

V režimu Receive [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) už nejsou všechny zprávy získané do vyrovnávací paměti předběžného načtení dostupné ve frontě a nacházejí se jenom ve vyrovnávací paměti předběžného načtení paměti, dokud je neobdrží do aplikace prostřednictvím rozhraní API **Receive** / **metody ReceiveAsync** nebo  / **OnMessageAsync** . Pokud se aplikace ukončí před přijetím zprávy do aplikace, nebudou se tyto zprávy nevratně ztratit.

V režimu Receive [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) se zprávy načtené do vyrovnávací paměti předběžného načtení získávají do vyrovnávací paměti v uzamčeném stavu a mají časový limit pro zatržení zámku. Pokud je vyrovnávací paměť předběžného načtení velká a zpracování trvá tak dlouho, že zámky zpráv vyprší, když se nachází ve vyrovnávací paměti předběžného načtení nebo i když aplikace zpracovává zprávu, může dojít k tomu, že aplikace bude zpracovávat některé matoucí události.

Aplikace může získat zprávu s vypršenou platností nebo bezprostředním vypršením platnosti zámku. V takovém případě může aplikace zprávu zpracovat, ale pak zjistí, že ji nemůže dokončit z důvodu vypršení platnosti zámku. Aplikace může kontrolovat vlastnost [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) (která je předmětem posunu hodin mezi zprostředkovatelem a časem místního počítače). Pokud platnost zámku zprávy vypršela, aplikace musí zprávu ignorovat. není třeba provést žádné volání rozhraní API ve zprávě ani se zprávou. Pokud platnost zprávy nevypršela, ale vypršení platnosti je bezprostřední, může zámek prodloužit a prodloužit jiným výchozím obdobím uzamčení voláním [zprávy. RenewLock ()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Pokud dojde k tichému vypršení platnosti zámku ve vyrovnávací paměti předběžného načtení, zpráva se považuje za opuštěnou a znovu ji zpřístupní pro načtení z fronty. To může způsobit, že se načte do vyrovnávací paměti předběžného načtení; umístěný na konci. Pokud vyrovnávací paměť předběžného načtení nemůže být obvykle zpracována během vypršení platnosti zprávy, způsobí to, že se zprávy opakovaně předběžně načítají, ale nikdy se neefektivně doručí do použitelného (v platném stavu) a po překročení maximálního počtu doručení se nakonec přesunou do fronty nedoručených zpráv.

Pokud potřebujete vysoký stupeň spolehlivosti pro zpracování zpráv a zpracování bude mít významnou práci a čas, doporučuje se používat funkci předběžného načtení nebo vůbec ne.

Pokud potřebujete vysokou propustnost a zpracování zpráv je běžně levné, přináší předběžné výhody významné výhody propustnosti.

Maximální počet předběžných načtení a doba trvání zámku nakonfigurované ve frontě nebo předplatném je třeba vyvážit tak, aby vypršel časový limit zámku minimálně v kumulativní očekávané době zpracování zprávy pro maximální velikost vyrovnávací paměti předběžného načtení a jedné zprávy. Zároveň by časový limit zámku neměl být tak dlouhý, aby zprávy překročily maximální [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) , když jsou náhodně vyřazené, takže jejich zámek před tím, než se znovu dokončí.

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
