---
title: Pořadí zpráv azure service bus a časová razítka | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak zachovat sekvenování a řazení (s časovými razítky) zpráv Azure Service Bus.
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
ms.openlocfilehash: 54d774c00fa650cb9608f46cc07b9d899709eaa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261655"
---
# <a name="message-sequencing-and-timestamps"></a>Určování pořadí zpráv a časová razítka

Sekvencování a časové razítko jsou dvě funkce, které jsou vždy povoleny na všech entitách service bus a povrchu prostřednictvím [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) a [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) vlastnosti přijatých nebo procházených zpráv.

Pro ty případy, ve kterých absolutní pořadí zpráv je významné a/nebo ve kterých spotřebitel potřebuje důvěryhodný jedinečný identifikátor pro zprávy, zprostředkovatel razítka zprávy s mezery, rostoucí pořadové číslo vzhledem k fronty nebo tématu. Pro dělené entity pořadové číslo je vydáno vzhledem k oddílu.

Hodnota **SequenceNumber** je jedinečné 64bitové celé číslo přiřazené ke zprávě, protože je přijímána a uložena zprostředkovatelem a funguje jako jeho vnitřní identifikátor. Pro rozdělené entity nejvyšší 16 bitů odráží identifikátor oddílu. Po vyčerpání rozsahu 48/64 bitů se pořadová čísla přetoču na nulu.

Pořadové číslo lze považovat za jedinečný identifikátor, protože je přiřazeno centrálním a neutrálním úřadem, nikoli klienty. To také představuje skutečné pořadí příjezdu, a je přesnější než časové razítko jako kritérium objednávky, protože časová razítka nemusí mít dostatečně vysoké rozlišení při extrémních rychlostech zpráv a může být předmětem (jakkoli minimální) hodiny zkosení v situacích, kdy makléř vlastnictví přechody mezi uzly.

Absolutní objednávka příjezdu je důležitá například v obchodních scénářích, kdy je omezený počet nabízených zboží dodáván podle pořadí přijíždějících hostů, dokud dodávky trvají; prodeje vstupenek na koncert jsou příkladem.

Funkce časového razítka funguje jako neutrální a důvěryhodná autorita, která přesně zachycuje čas utc doručení zprávy, která se odráží ve **vlastnosti EnqueuedTimeUtc.** Hodnota je užitečná, pokud obchodní scénář závisí na termínech, například zda byla pracovní položka odeslána k určitému datu před půlnocí, ale zpracování je daleko za nevyřízenými položkami fronty.

## <a name="scheduled-messages"></a>Naplánované zprávy

Zprávy můžete odeslat do fronty nebo tématu pro zpožděné zpracování. Můžete třeba naplánovat úlohu, která bude systému pro zpracování dostupná v konkrétní čas. Tato funkce realizuje spolehlivé distribuované plánovače podle času.

Naplánované zprávy se ve frontě nezhmotní, dokud nebude stanoven čas zařazení do fronty. Před tímto časem lze naplánované zprávy zrušit. Zrušení odstraní zprávu.

Zprávy můžete naplánovat nastavením [vlastnosti ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) při odesílání zprávy prostřednictvím cesty pravidelného odesílání nebo explicitně pomocí rozhraní [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API. Ten okamžitě vrátí naplánované zprávy **SequenceNumber**, které můžete později použít ke zrušení naplánované zprávy v případě potřeby. Naplánované zprávy a jejich pořadová čísla lze také zjistit pomocí [procházení zpráv](message-browsing.md).

**SequenceNumber** pro naplánovanou zprávu je platný pouze v době, kdy je zpráva v tomto stavu. Jako zpráva přechází do aktivního stavu, zpráva je připojena do fronty, jako by byla zařazena do fronty v aktuálním okamžiku, který zahrnuje přiřazení nové **SequenceNumber**.

Vzhledem k tomu, že funkce je ukotvena na jednotlivé zprávy a zprávy lze zařadit do fronty pouze jednou, Service Bus nepodporuje opakované plány pro zprávy.

## <a name="next-steps"></a>Další kroky

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)