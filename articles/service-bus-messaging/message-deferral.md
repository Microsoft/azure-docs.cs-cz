---
title: Odložení zpráv Azure Service Bus | Dokumentace Microsoftu
description: Odložit doručování zpráv služby Service Bus
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 4471b556dc1ac5f520185d7ad586fb489c6d8f30
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856804"
---
# <a name="message-deferral"></a>Odložení zpráv

Když klient fronty nebo odběru obdrží zprávu, že je připraven ke zpracování, ale pro zpracování není aktuálně možné kvůli zvláštní okolnosti uvnitř aplikace, má povolenou možnost "odložit" načtení zprávy, která se později. Zpráva zůstane ve frontě nebo odběru, ale odloží se bokem.

Odložení je funkce vytvořený specificky pro zpracování scénářů pracovního postupu. Pracovní postup rámce může vyžadovat určité operace zpracovat v určitém pořadí a může být nutné odložit zpracování některých přijaté zprávy, dokud se nedokončí předepsané předchozí práce, která je informován o další zprávy.

Jednoduchý příklad příkladů je pořadí zpracování pořadí, ve kterém oznámení platby od poskytovatele externí platby se zobrazí v systému dřív, než se rozšíří odpovídající objednávka ze storu na systém pro plnění. V takovém případě splnění systému může odložit zpracování platby oznámení, dokud nedojde k objednávku, pomocí kterého se má přidružit. Ve scénářích potkávací, kde zprávy z různých zdrojů jednotka vpřed pracovního postupu, pořadí zpracování v reálném čase ve skutečnosti může být správná, ale může do zpráv odráží výsledky mimo pořadí.

Nakonec odložení pomáhá při změny pořadí v pořadí doručení zprávy do pořadí, ve kterém může být zpracovány, a bezpečně ponechání těchto zpráv do úložiště zpráv, pro které zpracování musí být odložen.

## <a name="message-deferral-apis"></a>Odložení zpráv rozhraní API

Rozhraní API je [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) nebo [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) klienta rozhraní .NET Framework [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) v klientovi .NET Standard a **mesageReceiver.defer** nebo **messageReceiver.deferSync** v klientskou sadou Java. 

Odložené zprávy zůstávají ve frontě hlavní spolu s všechny ostatní aktivní zprávy (na rozdíl od nedoručených zpráv, kteří žijí v dílčí), ale může být již přijatých pomocí běžných příjmu/ReceiveAsync funkcí. Odložené zprávy můžete zjistit pomocí [procházení zpráv](message-browsing.md) Pokud aplikace ztratí sledování z nich.

Načíst odloženou zprávu, jeho vlastníka odpovídá zprávě o zapamatování [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) jako jeho odloží. Všechny příjemce, který zná pořadové číslo odloženou zprávu můžete později zpráva explicitně s `Receive(sequenceNumber)`.

Pokud zprávu nelze zpracovat, protože konkrétního prostředku pro zpracování této zprávy je dočasně nedostupná, ale neměli summarily pozastavit zpracování zpráv, je způsob, jak vložit této zprávy na straně pro několik minut, než pamatovat  **SequenceNumber** v [naplánovanou zprávu](message-sequencing.md) účtován za pár minut a znovu načíst odloženou zprávu dorazí naplánovanou zprávu. Pokud obslužná rutina zprávy závisí na databázi pro všechny operace a databáze je dočasně nedostupná, ji by měl nepoužívat odložení, ale spíše pozastavit příjem zpráv s úplně, dokud nebude databáze opět k dispozici.


## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)