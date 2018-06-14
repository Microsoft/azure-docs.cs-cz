---
title: Řazení zpráv Azure Service Bus a časová razítka | Microsoft Docs
description: Zachování pořadí zpráv Service Bus a pořadí časová razítka
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
ms.openlocfilehash: a67a9d01f686c6aa8a569239667ae14d7bf087a9
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28200730"
---
# <a name="message-sequencing-and-timestamps"></a>Zpráva sekvencování a časová razítka

Sekvencování a vytvoření časového razítka jsou dvě funkce, které jsou vždy povolena na všechny entity služby Service Bus a surface prostřednictvím [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) a [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) vlastností přijatých nebo Prohlédnuté zprávy.

Pro případy, ve kterých je důležité absolutní řazení zpráv a ve kterém příjemce musí trustworthy jedinečný identifikátor pro zprávy, zprávy razítka zprostředkovatele s mezery mezi bez, zvýšení pořadové číslo relativně k fronta nebo téma. Pro dělené entity je vydána pořadové číslo relativně k oddílu.

**SequenceNumber** hodnota je jedinečný 64bitové celočíselné přiřazen na zprávy, jako je přijatá a uložená zprostředkovatele a funkce jako jeho interní identifikátor. Pro dělené entity projeví nejhornější 16 bitů identifikátor oddílu. Pořadová čísla přejít na nule při vyčerpání 48 nebo 64bitový rozsahu.

Vzhledem k tomu, že je přiřazen autoritou centrální a neutrální a ne klientů, může být důvěryhodný jako jedinečný identifikátor pořadové číslo. Také představuje true pořadí doručení a je přesnější než časové razítko jako kritérium pořadí, protože nemusí mít dostatečné řešení tempem extrémně zpráva časová razítka a mohou podléhat (ale minimální) hodiny zkosení v situacích, kdy zprostředkovatel vlastnictví přechází mezi uzly.

Záleží na pořadí absolutní doručení, například v obchodní scénáře, ve kterých omezený počet nabízené zboží se zpracovávají na základě přijde first dřív při zdroje poslední; Prodej lístků vzájemné součinnosti jsou příklad.

Funkce časového razítka funguje jako neutrální a důvěryhodné autority, která přesně zaznamená čas UTC doručení zpráv, promítnuta **EnqueuedTimeUtc** vlastnost. Hodnota je užitečné, pokud podnikový scénář závisí na konečné termíny, například zda byla odeslána pracovní položku v určitý den před půlnoc, ale zpracování je daleko za nevyřízené položky fronty.

## <a name="scheduled-messages"></a>Plánované zprávy

Můžete odeslat zprávy a pokuste se fronta nebo téma pro zpožděné zpracování; Chcete-li například naplánovat úlohu k dispozici pro zpracování systémem v určitém čase. Tato funkce rozpoznává spolehlivé distribuované scheduler založené na čase.

Naplánované zprávy ve frontě není vyhodnotit až do okamžiku definované zařazování. Před uplynutím této doby se dají zrušit naplánované zprávy. Zrušení odstraní zprávu.

Můžete naplánovat zprávy buď nastavením [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) vlastnost při odesílání zprávy prostřednictvím regulární odchozí cestě nebo explicitně s [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) rozhraní API. K tomu okamžitě vrátí naplánované zprávu **SequenceNumber**, který můžete později použít ke zrušení naplánované zprávy v případě potřeby. Naplánované zpráv a jejich pořadová čísla můžete také zjistit pomocí [procházení zpráva](message-browsing.md).

**SequenceNumber** pro naplánované zpráva je pouze platné při zpráva je v tomto stavu. Jako zpráva přechody do aktivního stavu, zpráva se připojí k frontě jako kdyby je zařazených do fronty na aktuální rychlých, která zahrnuje přiřazení nové **SequenceNumber**.

Protože tato funkce je ukotven na jednotlivé zprávy a zprávy lze pouze zařazených do fronty jednou, Service Bus nepodporuje opakující se plány pro zprávy.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)