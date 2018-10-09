---
title: Procházení zpráv v Azure Service Bus | Dokumentace Microsoftu
description: Procházet a prohlížení zpráv služby Service Bus
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
ms.date: 09/25/2018
ms.author: spelluru
ms.openlocfilehash: 7ce2e870be0178420d80682bd18adbef814c162f
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857461"
---
# <a name="message-browsing"></a>Procházení zpráv

Procházení zpráv nebo prohlížení, umožňuje klientovi služby Service Bus se vytvořit výčet všech zpráv, které jsou umístěny do fronty nebo odběru, obvykle pro účely ladění a diagnostiky.

Operace peek vrátí všechny zprávy, které existují ve frontě nebo odběru protokolu zpráv, nejen ty, k dispozici pro okamžité získání s `Receive()` nebo `OnMessage()` smyčky. `State` Vlastnosti každé zprávy zjistíte, jestli je aktivní zprávy (k dispozici pro další přijetí), [odložené](message-deferral.md), nebo [naplánované](message-sequencing.md).

Spotřebované a vypršela platnost zprávy vyčistily pomocí asynchronního "úklid" spustit a nemusí být přesně zprávy vypršení platnosti a proto `Peek` může ve skutečnosti vrátí zprávy, které již vypršela a že se odeberou nebo dead lettered při operace přijetí je vyvolána další ve frontě nebo odběru.

To je obzvláště důležité vzít v úvahu při pokusu o obnovení odložené zprávy z fronty. Zpráva, pro kterou [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) uplynutí okamžiku už nejsou vhodné pro pravidelné načítání jiným způsobem, i v případě, že je vracených náhled. Vrací tyto zprávy je záměrné, protože náhled je diagnostický nástroj, který odráží aktuální stav do protokolu.

Náhled vrátí zprávy, které byly uzamčeny a jsou právě zpracovávána pomocí dalších příjemců, ale ještě nebyly dokončeny. Ale protože Peek vrací odpojené snímku, stav zámku zprávy nebyly nalezeny u nahlédnout zpráv a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) a [token LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) throw vlastnosti [ InvalidOperationException](/dotnet/api/system.invalidoperationexception) když se aplikace pokusí přečíst.

## <a name="peek-apis"></a>Náhled rozhraní API

[Náhled/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) a [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) metody existují v klientské knihovny pro všechny .NET a Javu a pro všechny objekty příjemce: **MessageReceiver**, **Popsaným**, **QueueClient**, a **SubscriptionClient**. Operace Peek funguje pro všechny fronty a předplatná a jejich příslušné fronty nedoručených zpráv.

Při volání opakovaně, metody Peek zobrazí všechny zprávy, které existují v protokolu fronty nebo odběru v pořadí podle čísla pořadí, od nejnižší dostupné pořadové číslo k nejvyšší. To je v tom pořadí, ve kterém byly zpráv zařazených do fronty a není pořadí, ve kterém může být nakonec načten zprávy.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) načte více zpráv a vrátí je jako výčet. Pokud nejsou k dispozici žádné zprávy, výčet objekt je prázdný, není null.

Dosazením přetížení metody [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) ve kterém se má spustit a pak volat přetížení bez parametrů metody pro výčet Další. **PeekBatch** ekvivalentně funkce, ale všechny najednou načte sadu zpráv.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
