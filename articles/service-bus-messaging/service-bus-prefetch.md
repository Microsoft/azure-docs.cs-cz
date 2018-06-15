---
title: Zprávy předběžné načtení Azure Service Bus | Microsoft Docs
description: Vylepšit výkon prefetching zpráv Azure Service Bus.
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
ms.openlocfilehash: 0a61918108a48f4a9fa3d1c07cc8d41525f1f2a0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28928157"
---
# <a name="prefetch-azure-service-bus-messages"></a>Předběžné načtení zpráv Azure Service Bus

Při *předběžné načtení* je povolena v některém z oficiálního klientů služby Service Bus příjemce tiše získá další zprávy, až [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) limit, nad rámec co aplikace původně žádali.

Jeden počáteční [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) nebo [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) volání proto získá zprávu pro okamžitou spotřebu, která je vrácena nejbližším jako dostupné. Další Klient pak získá zprávy na pozadí, aby vyplnilo předběžné načtení ve vyrovnávací paměti.

## <a name="enable-prefetch"></a>Umožnit předběžné načtení

Pomocí rozhraní .NET, povolíte funkci předběžné nastavením [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) vlastnost **MessageReceiver**, **QueueClient**, nebo **SubscriptionClient**  na číslo větší než nula. Nastavení hodnoty na nulové vypne předběžné načtení.

Toto nastavení můžete snadno přidat na straně příjmu z [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) nebo [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) nastavení ukázky Pokud chcete vidět v tomto kontextu.

Zprávy jsou k dispozici v předběžné načtení ve vyrovnávací paměti, všechny následné **Receive**/**ReceiveAsync** volání jsou okamžitě splněny z vyrovnávací paměti a velikost vyrovnávací paměti je doplněny v Jakmile místo k dispozici na pozadí. Pokud nejsou k dispozici pro doručení žádné zprávy, operace příjmu vyprázdní vyrovnávací paměti a potom počká nebo bloky, podle očekávání.

Předběžné načtení také funguje stejně jako s [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) a [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) rozhraní API.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Pokud je rychlejší, proč je předběžné načtení není výchozí možnost?

Předběžné načtení urychluje tok zpráv tak, že zprávy pro místní načtení snadno dostupné, když a předtím, než aplikace požádá o jednu. Tento nárůst propustnosti je výsledkem na druhou stranu, která musí explicitně vytváření aplikací:

Pomocí [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode.receiveanddelete) přijímat režimu, všechny zprávy, které získáte do vyrovnávací paměti předběžné načtení již nejsou k dispozici ve frontě a pouze jsou umístěny ve vyrovnávací paměti předběžné načtení v paměti, dokud nedorazí k příjemci do aplikace prostřednictvím **přijímat**/**ReceiveAsync** nebo **OnMessage**/**OnMessageAsync** rozhraní API. Pokud aplikace ukončí před obdržení zpráv do aplikace, tyto zprávy se nenávratně ztratí.

V [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode.peeklock) přijímat režimu, zprávy načtených do vyrovnávací paměti pro předběžný jsou získali do vyrovnávací paměti v uzamčeném stavu a mít hodiny časový limit pro obaly zámku. Pokud se předběžné načtení ve vyrovnávací paměti je velký a zpracování trvá tak dlouho tuto zprávu, že zámky vyprší, při které se nacházejí ve vyrovnávací paměti předběžné načtení nebo i aplikace je zpracování zprávy, může být některé matoucí události pro aplikaci pro zpracování.

Aplikace může získat zprávu s zámek vypršela platnost, nebo imminently, jejichž platnost brzy vyprší. Pokud ano, aplikace může zpracovat zprávu, ale pak vyhledejte, že ho nelze dokončit z důvodu vypršení zámku. Aplikace můžete zkontrolovat [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) (který podléhá hodiny zkosení mezi zprostředkovatele a hodiny místní počítač). Pokud zpráva zámek vypršela platnost, aplikace musí ignorovat message; žádná volání rozhraní API na nebo se zprávou musí provést. Pokud zpráva nevypršela, ale vypršení platnosti je bezprostředního, zámek můžete obnovit a rozšířit pomocí jiné výchozí dobu uzamčení voláním [zprávy. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Pokud zámek bezobslužně ve vyrovnávací paměti předběžné načtení vyprší, je zpracovaná jako opuštění zpráva a je znovu k dispozici pro načtení z fronty. Které může způsobit ho k vyvolání do vyrovnávací paměti předběžné načtení; umístit na konci. Pokud předběžné načtení ve vyrovnávací paměti nelze obvykle fungovala prostřednictvím při vypršení platnosti zprávy, to způsobí, že zpráv, které mají být opakovaně prefetched, ale nikdy efektivně doručené v použitelného stavu (řádně uzamčeném) a nakonec přesunou do fronty nedoručených zpráv jednou počet maximální doručení bylo překročeno.

Pokud potřebujete vysokou úroveň spolehlivosti pro zpracování zprávy a zpracování trvá velice pracné a čas, se doporučuje použít funkci předběžné načtení, můžete, nebo vůbec.

Pokud potřebujete základní v celé a zpracování zprávy je běžně levných, vypočítá předběžné načtení propustnost významné výhody.

Předběžné načtení maximální počet a dobu trvání uzamčení na fronty nebo předplatné konfiguraci muset vyváženy tak, aby se časový limit uzamčení alespoň překračuje kumulativní očekávaná zpráva zpracování času pro maximální velikost vyrovnávací paměti předběžné načtení plus jednu zprávu. Ve stejnou dobu, časový limit uzamčení by mělo není trvat dlouho, zprávy může být vyšší než jejich maximální [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) omylem jsou vyřazena, proto nutnosti jejich zámku vyprší před se víckrát.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
