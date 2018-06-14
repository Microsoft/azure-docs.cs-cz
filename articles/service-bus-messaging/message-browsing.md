---
title: Azure Service Bus zprávu procházení | Microsoft Docs
description: Procházet a prohlížet zprávy služby Service Bus
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
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: 124c4592a41bf9f3e2a148ba5c3b928bb051d160
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28196616"
---
# <a name="message-browsing"></a>Procházení zpráv

Zpráva procházení ("prohlížení") umožňuje klientovi služby Service Bus se vytvořit výčet všechny zprávy, které se nacházejí v fronty nebo předplatného, obvykle pro diagnostiku a účely ladění.

Funkce Náhled operace, které vrátí všechny zprávy, které existují v fronty nebo předplatné protokolu zpráv, ne jenom ty k dispozici pro okamžité pořízení s `Receive()` nebo `OnMessage()` smyčky. `State` Vlastnost každá zpráva informuje, zda je zpráva aktivní (k dispozici pro další přijetí), [odložené](message-deferral.md), nebo [naplánované](message-sequencing.md).

Spotřebované a jeho platnost zprávy se vyčistily podle asynchronní "uvolňování" spustit a nemusí nutně přesně při zprávy vyprší a proto `Peek` může vrátit skutečně zprávy, které již vypršela platnost a bude odebrána nebo v případě lettered zpráv operace příjmu je volána vedle fronty nebo předplatné.

To je obzvláště důležité, třeba vzít v úvahu při pokusu o obnovení odložené zpráv z fronty. Zpráva, pro kterou [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) rychlých uplynutí již není vhodné pro regulární načtení jiným způsobem, i když se vrátí funkce Náhled. Vrací tyto zprávy je záměrné, protože funkce Náhled diagnostického nástroje aktuálním stavu protokolu.

Funkce Náhled také vrátí hodnotu zprávy, které byly uzamčen a jsou právě zpracovávány ostatní příjemce, ale ještě nebyly dokončeny. Ale protože funkce Náhled vrátí odpojené snímku, stav zámku zprávy nemůže být dodržen na provedla zprávy a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) a [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) vlastnosti throw [ InvalidOperationException](/dotnet/api/system.invalidoperationexception) když se aplikace pokusí přečíst je.

## <a name="peek-apis"></a>Prohlížení rozhraní API

[Funkce Náhled/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) a [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) metody existují ve všech rozhraní .NET a Javu knihovny klienta a pro všechny objekty příjemce: **MessageReceiver**, **MessageSession**, **QueueClient**, a **SubscriptionClient**. Prohlížení funguje na všech front a předplatných a jejich odpovídajících fronty nedoručených zpráv.

Při volání opakovaně, metoda funkce Náhled zobrazí všechny zprávy, které existují v protokolu fronty nebo předplatného v číslo pořadí pořadí od nejnižší dostupné pořadové číslo na ty nejvyšší. Toto je pořadí, ve kterém byly zpráv zařazených do fronty; není pořadí, ve kterém může nakonec načíst zprávy.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) načte více zpráv a vrací je jako výčet. Pokud jsou k dispozici žádné zprávy, objekt výčtu je prázdné, není null.

Můžete také počáteční hodnoty přetížení metody s [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , pro kterou chcete spustit a pak zavolají přetížení bez parametrů metody pro výčet Další. **PeekBatch** ekvivalentně funguje, ale všechny najednou načte sadu zpráv.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)