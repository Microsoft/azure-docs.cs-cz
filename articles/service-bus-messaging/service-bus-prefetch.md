---
title: Azure Service Bus předběžné načtení zpráv | Dokumentace Microsoftu
description: Vylepšení výkonu prostřednictvím předběžné načítání zpráv Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: a61b7b08d883c1b5a7fde93c249fc8de1473d15d
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42060537"
---
# <a name="prefetch-azure-service-bus-messages"></a>Předběžné načtení zpráv Azure Service Bus

Když *předběžné načtení* je povolená v některém z oficiální klienti služby Service Bus, příjemce tiše získá další zprávy, až [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) limit, co aplikace zpočátku zobrazí výzva pro.

Jeden počáteční [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) nebo [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) volání proto získá zprávu pro okamžité využití, která je vrácena jako k dispozici co nejdříve. Další Klient pak získá zprávy tak, aby vyplnil předběžné načtení ve vyrovnávací paměti na pozadí.

## <a name="enable-prefetch"></a>Umožnit předběžné načtení

S .NET povolíte nastavením předběžné funkce [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) vlastnost **MessageReceiver**, **QueueClient**, nebo **SubscriptionClient**  na číslo větší než nula. Nastavením této hodnoty na nulové vypne předběžné načtení.

Toto nastavení můžete snadno přidat na straně příjmu [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) nebo [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) ukázky nastavení projevily v těchto kontextech.

Zprávy jsou k dispozici v předběžné načtení ve vyrovnávací paměti, jakákoli následná **Receive**/**ReceiveAsync** volání okamžitě splnění z vyrovnávací paměti a vyrovnávací paměť je doplněny v na pozadí jako místo k dispozici. Pokud k doručování nejsou žádné zprávy, operace obdržení vyprázdní vyrovnávací paměť a potom čeká nebo bloky, podle očekávání.

Předběžné načtení také funguje stejně jako s [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) a [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) rozhraní API.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Pokud je rychlejší, proč není předběžné načtení ve výchozí možnost?

Předběžné načtení zrychluje tok zpráv tak, že snadno k dispozici místní zprávy, když a předtím, než aplikace požádá o jednu. Toto zvýšení propustnosti je výsledkem o kompromis, který vytváření aplikací musíte provést explicitně:

S [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) přijímat režimu, všechny zprávy, které jsou získány do vyrovnávací paměti předběžné načtení už nejsou k dispozici ve frontě a pouze jsou umístěny ve vyrovnávací paměti předběžné načtení v paměti, dokud nedorazí k příjemci do aplikace až **přijímat**/**ReceiveAsync** nebo **OnMessage**/**OnMessageAsync** rozhraní API. Ukončení aplikace předtím, než jsou zprávy přijímány do aplikace, tyto zprávy se nenávratně ztratí.

V [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) přijímat režimu, načíst do předběžného načítání vyrovnávací paměti zpráv pořízené do vyrovnávací paměti v uzamčeném stavu a vypršení časového limitu účtovat poplatky za tikání zámku. Pokud je velká předběžné načtení ve vyrovnávací paměti a zpracování trvá tak dlouho této zprávy, že vyprší během které se nacházejí v předběžné načtení ve vyrovnávací paměti nebo dokonce i za běhu aplikace zpracovává zprávu, může být některé události matoucí pro aplikaci pro zpracování.

Aplikace může získat zprávy s vypršenou platností nebo imminently, u nichž vyprší platnost zámku. Pokud ano, aplikace může zpracovat zprávu, ale pak najdete, že jej nelze dokončit z důvodu platnost zámku. Aplikace můžete zkontrolovat, [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) vlastnost (která je v souladu s posun mezi zprostředkovatele a hodiny v místním počítači hodin). Pokud vypršela platnost zámku zprávy, aplikace musí ignorovat zprávy. třeba žádná volání rozhraní API nebo se zprávou. Pokud zpráva není vypršel, ale doba platnosti má bezprostřední, zámek můžete obnovit a rozšířit pomocí jiné výchozí dobu uzamčení voláním [zprávy. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Pokud zámek tiše ve vyrovnávací paměti předběžné načtení vyprší, zpráva je zpracováván jako opuštěnou a je znovu k dispozici pro načtení z fronty. Umožňuje načíst do předběžné načtení ve vyrovnávací paměti; který může způsobit umístěné na konci. Pokud vyrovnávací paměť předběžné načítání nelze pracovali obvykle prostřednictvím při vypršení platnosti zpráv, to způsobí, že mají být použitelné (řádně) zamknuté opakovaně předem načteného, ale nikdy efektivně doručené zprávy a nakonec přesunou do fronty nedoručených zpráv jednou byl překročen maximální počet doručení.

Pokud potřebujete vysokou úroveň spolehlivosti pro zpracování zpráv a zpracování trvá pracné a čas, doporučujeme použít funkci předběžné načtení obdržíte, nebo vůbec ne.

Pokud potřebujete vyšší v průběhu a zpracování zpráv je běžně levné, provede předběžné načtení propustnost významné výhody.

Předběžné načtení maximální počet a doba trvání uzamknutí hodnocen fronty nebo odběru musí vyváženy tak, že časový limit zámku alespoň překračuje kumulativní očekávaná zpráva zpracování času pro maximální velikost vyrovnávací paměti předběžné načtení plus jedna zpráva. Ve stejnou dobu, časový limit zámku by mělo být neměl by se tak dlouho, zprávy mohou být delší než jejich maximální [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) omylem se zahodí, proto by jejich zámku vyprší před právě víckrát.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
