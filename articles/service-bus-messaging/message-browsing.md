---
title: Azure Service Bus – procházení zpráv
description: Procházet a prohlížet zprávy service bus umožňuje klienta Služby Azure Service Bus vytvořit výčet všech zpráv, které jsou umístěny ve frontě nebo předplatné.
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
ms.openlocfilehash: 6156557d10210535b287aa516070c0b5da416512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539361"
---
# <a name="message-browsing"></a>Procházení zpráv

Procházení zpráv nebo prohlížení umožňuje klientovi služby Service Bus vytvořit výčet všech zpráv, které jsou umístěny ve frontě nebo předplatném, obvykle pro účely diagnostiky a ladění.

Operace náhledu vrátí všechny zprávy, které existují v protokolu zpráv fronty `Receive()` nebo `OnMessage()` odběr, nikoli pouze ty, které jsou k dispozici pro okamžité získání s nebo smyčky. Vlastnost `State` každé zprávy informuje o tom, zda je zpráva aktivní (k dispozici k přijetí), [odložená](message-deferral.md)nebo [plánovaná](message-sequencing.md).

Spotřebované a vypršela platnost zprávy jsou vyčištěny asynchronní "uvolňování paměti" spustit `Peek` a ne nutně přesně při vypršení platnosti zprávy a proto může skutečně vrátit zprávy, které již vypršela platnost a budou odebrány nebo nedoručené při přijetí operace je další vyvolána ve frontě nebo odběr.

To je obzvláště důležité mít na paměti při pokusu o obnovení odložené zprávy z fronty. Zpráva, pro kterou [expiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) instant prošel již není způsobilá pro pravidelné načítání jinými prostředky, i když je vrácena Peek. Vrácení těchto zpráv je záměrné, protože Peek je diagnostický nástroj odrážející aktuální stav protokolu.

Peek také vrátí zprávy, které byly uzamčeny a jsou aktuálně zpracovávány jinými příjemci, ale ještě nebyly dokončeny. Však protože Peek vrátí odpojený snímek, stav zámku zprávy nelze pozorovat na inkresované zprávy a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) a [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) vlastnosti vyvolat [InvalidOperationException](/dotnet/api/system.invalidoperationexception) při aplikaci pokusí o jejich čtení.

## <a name="peek-apis"></a>Náhled api

Metody [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) a [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) existují ve všech klientských knihovnách .NET a Java a na všech objektech příjemce: **MessageReceiver**, **MessageSession**. Náhled funguje na všechny fronty a odběry a jejich příslušné fronty nedoručených zpráv.

Při opakovaném volání peek metoda vyjmenovává všechny zprávy, které existují v protokolu fronty nebo odběr, v pořadí pořadí, od nejnižší dostupné pořadové číslo na nejvyšší. Toto je pořadí, ve kterém byly zprávy zařazeny do fronty a není pořadí, ve kterém zprávy mohou být nakonec načteny.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) načte více zpráv a vrátí je jako výčet. Pokud nejsou k dispozici žádné zprávy, je objekt výčtu prázdný, nikoli null.

Můžete také zasít přetížení metody s [SequenceNumber,](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) na kterém chcete spustit a potom volat parametrless přetížení metody výčet dále. **PeekBatch** funguje ekvivalentní, ale načte sadu zpráv najednou.

## <a name="next-steps"></a>Další kroky

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
