---
title: Azure Service Bus přednačítá zprávy | Dokumenty společnosti Microsoft
description: Zlepšete výkon předběžným načítáním zpráv služby Azure Service Bus. Zprávy jsou snadno dostupné pro místní načítání před žádosti aplikace pro ně.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 80717ab940d27e9bf108b3740309bcd7d71668fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760653"
---
# <a name="prefetch-azure-service-bus-messages"></a>Předběžné načtení zpráv služby Azure Service Bus

Pokud *předběžné načtení* je povoleno v některém z oficiálních klientů service bus, příjemce tiše získá další zprávy, až do limitu [PrefetchCount,](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) nad rámec toho, co aplikace původně požádal.

Jeden počáteční [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) nebo [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) volání proto získá zprávu pro okamžitou spotřebu, která je vrácena, jakmile je k dispozici. Klient pak získá další zprávy na pozadí, vyplnit vyrovnávací paměti předběžného načtení.

## <a name="enable-prefetch"></a>Povolit předběžné načtení

S rozhraním .NET povolíte funkci Předběžné načtení nastavením vlastnosti [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) **messagereceiver**, **QueueClient**nebo **SubscriptionClient** na číslo větší než nula. Nastavení hodnoty na nulu vypne předběžné načtení.

Toto nastavení můžete snadno přidat na straně příjmu [queuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) nebo [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) ukázky 'nastavení zobrazit efekt v těchto kontextech.

Zatímco zprávy jsou k dispozici ve vyrovnávací paměti předběžného načtení, všechny následné **Receive**/**ReceiveAsync** volání jsou okamžitě splněny z vyrovnávací paměti a vyrovnávací paměti je doplněna na pozadí jako místo k dispozici. Pokud nejsou k dispozici žádné zprávy pro doručení, operace příjmu vyprázdní vyrovnávací paměť a pak čeká nebo blokuje, podle očekávání.

Předběžné načtení funguje také stejným způsobem s [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) a [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) API.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Pokud je rychlejší, proč není předběžné načtení výchozí možností?

Předběžné načtení urychluje tok zpráv tím, že zpráva je snadno dostupná pro místní načítání, když a předtím, než o ni aplikace požádá. Tento nárůst propustnostje výsledkem kompromisu, který musí autor aplikace explicitně provést:

S [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu příjmu všechny zprávy, které jsou získány do vyrovnávací paměti předběžného načtení již nejsou k dispozici ve frontě a jsou umístěny pouze v paměti prefetch vyrovnávací paměti, dokud jsou přijaty do aplikace prostřednictvím/**ReceiveReceiveAsync** nebo **Receive** **OnMessage**/**OnMessageAsync** ROZHRANÍ API. Pokud aplikace ukončí před zprávy jsou přijaty do aplikace, tyto zprávy jsou nenávratně ztraceny.

V režimu příjmu [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) zprávy načtené do vyrovnávací paměti předběžného načtení jsou získány do vyrovnávací paměti v uzamčeném stavu a mají časový čas pro zaškrtávání zámku. Pokud je vyrovnávací paměť předběžného načtení velká a zpracování trvá tak dlouho, že vyprší platnost zámků zpráv při načítání ve vyrovnávací paměti předběžného načtení nebo i v době, kdy aplikace zpracovává zprávu, může být některé matoucí události pro aplikaci ke zpracování.

Aplikace může získat zprávu s vypršením nebo bezprostředně končící zámek. Pokud ano, aplikace může zprávu zpracovat, ale pak zjistíte, že ji nelze dokončit z důvodu vypršení platnosti zámku. Aplikace můžete zkontrolovat [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) vlastnost (která je předmětem hodiny zkosení mezi zprostředkovatele a místní hodiny počítače). Pokud vypršela platnost zámku zprávy, aplikace musí zprávu ignorovat. žádné volání rozhraní API nebo se zprávou by měla být provedena. Pokud zpráva není vypršela, ale vypršení platnosti je bezprostřední, zámek lze obnovit a rozšířit o další výchozí období uzamčení voláním [zprávy. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Pokud zámek tiše vyprší ve vyrovnávací paměti předběžného načtení, zpráva je považována za opuštěnou a je znovu k dispozici pro načtení z fronty. To může způsobit, že bude načten do vyrovnávací paměti předběžného načtení; na konci. Pokud vyrovnávací paměť předběžného načtení nelze obvykle pracovat prostřednictvím během vypršení platnosti zprávy, to způsobí, že zprávy, které mají být opakovaně předběžně načteny, ale nikdy efektivně doručena v použitelném (platně uzamčen) stavu a nakonec přesunuty do fronty nedoručených zpráv, jakmile maximální počet dodávek.

Pokud potřebujete vysoký stupeň spolehlivosti pro zpracování zpráv a zpracování vyžaduje významnou práci a čas, je doporučeno použít funkci předběžného načtení konzervativně nebo vůbec.

Pokud potřebujete vysokou propustnost a zpracování zpráv je obvykle levné, předběžné načtení poskytuje významné výhody propustnosti.

Maximální počet předběžného načtení a doba trvání zámku nakonfigurovaná ve frontě nebo předplatném musí být vyvážena tak, aby časový limit uzamčení alespoň překročil kumulativní očekávanou dobu zpracování zprávy pro maximální velikost vyrovnávací paměti předběžného načtení plus jednu zprávu. Ve stejné době časový limit uzamčení by neměla být tak dlouhá, že zprávy mohou překročit jejich maximální [TimeToLive,](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) když jsou omylem vynechány, což vyžaduje, aby jejich zámek vyprší před opětovnou doručení.

## <a name="next-steps"></a>Další kroky

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
