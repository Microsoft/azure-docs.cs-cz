---
title: Azure Service Bus – odložení zprávy
description: Tento článek vysvětluje, jak odložit doručení zpráv Azure Service Bus. Zpráva zůstane ve frontě nebo odběru, ale odloží se bokem.
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
ms.openlocfilehash: 5e32c461902c1e340c6cece22669a59847e660cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538392"
---
# <a name="message-deferral"></a>Odložení zpráv

Když fronta nebo klient předplatného obdrží zprávu, že je ochoten zpracovat, ale pro které zpracování není aktuálně možné z důvodu zvláštních okolností uvnitř aplikace, má možnost "odložení" načítání zprávy do pozdějšího bodu. Zpráva zůstane ve frontě nebo odběru, ale odloží se bokem.

Časově rozlišená funkce je funkce speciálně vytvořená pro scénáře zpracování pracovního postupu. Rámce pracovního postupu mohou vyžadovat zpracování určitých operací v určitém pořadí a může být nutné odložit zpracování některých přijatých zpráv, dokud nebude dokončena předepsaná předchozí práce, která je informována jinými zprávami.

Jednoduchým ilustrativním příkladem je pořadí zpracování objednávek, ve kterém se oznámení o platbě od externího poskytovatele plateb zobrazí v systému před tím, než byla odpovídající nákupní objednávka rozšířena z přední strany obchodu do systému plnění. V takovém případě může systém plnění odložit zpracování oznámení o platbě, dokud nebude existovat příkaz, ke kterému jej chcete přidružit. Ve scénářích schůzky, kde zprávy z různých zdrojů řídit pracovní postup vpřed, pořadí provádění v reálném čase může být skutečně správné, ale zprávy odrážející výsledky může dorazit mimo pořadí.

Nakonec odklad pomůcky při změně pořadí zpráv z pořadí doručení do pořadí, ve kterém mohou být zpracovány, přičemž tyto zprávy bezpečně v úložišti zpráv, pro které je třeba odložit zpracování.

## <a name="message-deferral-apis"></a>Api časově rozlišených zpráv

Rozhraní API je [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) nebo [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) v klientovi rozhraní .NET Framework, [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) v klientovi .NET Standard a [IMessageReceiver.defer](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) nebo [IMessageReceiver.deferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) v klientovi Java. 

Odložené zprávy zůstávají v hlavní frontě spolu se všemi ostatními aktivními zprávami (na rozdíl od nedoručených zpráv, které žijí v podfrontě), ale již nemohou být přijaty pomocí běžných funkcí Receive/ReceiveAsync. Odložené zprávy lze zjistit prostřednictvím [procházení zpráv,](message-browsing.md) pokud aplikace ztratí přehled o nich.

Chcete-li načíst odložené zprávy, jeho vlastník je zodpovědný za zapamatování [SequenceNumber,](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) jak ji odkládá. Každý příjemce, který zná pořadové číslo odložené zprávy `Receive(sequenceNumber)`může později přijímat zprávu explicitně s .

Pokud zprávu nelze zpracovat, protože konkrétní prostředek pro zpracování této zprávy je dočasně nedostupný, ale zpracování zpráv by neměla být souhrnně pozastavena, způsob, jak umístit tuto zprávu na stranu na několik minut je pamatovat **SequenceNumber** v [naplánované zprávy,](message-sequencing.md) které mají být zaúčtovány během několika minut a znovu načíst odložené zprávy při doručení naplánované zprávy. Pokud obslužná rutina zprávy závisí na databázi pro všechny operace a tato databáze je dočasně nedostupná, neměla by používat časově rozlišovací, ale spíše úplně pozastavit příjem zpráv, dokud nebude databáze znovu k dispozici.


## <a name="next-steps"></a>Další kroky

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
