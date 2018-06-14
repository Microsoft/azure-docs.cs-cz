---
title: Odložení zpráv Azure Service Bus | Microsoft Docs
description: Odložení doručování zpráv služby Service Bus
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
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: bece2be88a020610dfd3d22f15f7d276d99bb153
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28196971"
---
# <a name="message-deferral"></a>Odložení zpráv

Když fronty nebo předplatné klient obdrží zprávu, že je ochoten zpracovat, ale pro zpracování, který není aktuálně možné kvůli zvláštní okolnosti uvnitř aplikace, má možnost "odložit" načtení zprávy na bod novější. Zpráva zůstane ve frontě nebo předplatného, ale jeho je vyhrazena.

Odložení je funkce vytvořený specificky pro zpracování scénáře pracovního postupu. Architektury pracovního postupu může vyžadovat určité operace, které mají být zpracovány v určitém pořadí a může být nutné odložit zpracování některých přijatých zpráv, dokud se nedokončí předepsaných předchozí práci, kterou je o tom informováni prostřednictvím další zprávy.

Jednoduchý příklad ilustrativní pro tento je pořadí zpracování pořadí, ve kterém oznámení platby od poskytovatele platebních externí se nachází v rámci systému před odpovídající nákupní objednávka nebyly rozšířeny. zpředu úložiště systému splnění žádosti. V takovém případě může systém splnění odložení zpracování platebních oznámení, dokud nedojde pořadí, ke které chcete přidružit ho. V potkávací scénáře, kde zprávy z různých zdrojů jednotka pracovní postup směrem vpřed, pořadí zpracování v reálném čase může být skutečně správný, ale může doručování zpráv, které odráží výstupy mimo pořadí.

Nakonec odložení pomůcky v znovu zprávy z příchodem pořadí řazení do pořadí, ve kterém může být zpracovány, a bezpečně nechat těchto zpráv v úložišti zpráv, pro které zpracování musí být odložen.

## <a name="message-deferral-apis"></a>Zpráva odložení rozhraní API

Rozhraní API je [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) nebo [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) v rozhraní .NET Framework klientovi [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) v rozhraní .NET standardní klienta, a **mesageReceiver.defer** nebo **messageReceiver.deferSync** v jazyce Java klienta. 

Odložené zprávy zůstanou v hlavní fronty spolu s všechny ostatní aktivní zprávy (na rozdíl od nedoručených zpráv, kteří žijí v dílčí fronta), ale může být už přijatých pomocí regulárních Receive/ReceiveAsync funkce. Odložené zprávy můžete zjistit prostřednictvím [zpráva procházení](message-browsing.md) Pokud aplikace ztratí sledovat z nich.

Načíst zprávu odložené, jeho vlastníka zodpovídá za pamatovat [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) jako jeho odkládat údaje. Všechny příjemce, že zná pořadové číslo zprávy, které odložené můžete později zobrazí se zpráva explicitně s `Receive(sequenceNumber)`.

Pokud zprávu nelze zpracovat, protože je dočasně nedostupná konkrétní prostředků pro zpracování zprávy, ale zpracování zpráv by neměly být pozastaveno summarily, je způsob, jak put zprávy na straně pro několik minut pamatovat  **SequenceNumber** v [naplánované zpráva](message-sequencing.md) odeslat za pár minut a znovu načíst odložené zprávy při doručení naplánované zprávy. Všimněte si, že pokud obslužné rutiny zpráv závisí na databázi pro všechny operace, a že je databáze dočasně nedostupná, by měl nepoužívat odložení, ale místo pozastavit přijímání zpráv úplně, dokud nebude databáze znovu k dispozici.

Odložit zprávy nemá negativní vliv na vypršení platnosti zprávy, což znamená, že odložené zprávy stále vyprší v původně naplánovanou dobu přesunou do fronty nedoručených zpráv na konfiguraci.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)