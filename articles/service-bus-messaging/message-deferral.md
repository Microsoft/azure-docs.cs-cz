---
title: Odložení zpráv Azure Service Bus | Dokumentace Microsoftu
description: Odložit doručování zpráv služby Service Bus
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
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: efe04b19188d7324c3f86565610040b8eaa97c43
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855387"
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

Odložené zprávy nemá vliv na vypršení platnosti zpráv, což znamená, že odložené zprávy stále v původně naplánovanou dobu vypršení platnosti jsou poté přesunut do fronty nedoručených zpráv, pokud příslušné funkce konfigurovány.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)