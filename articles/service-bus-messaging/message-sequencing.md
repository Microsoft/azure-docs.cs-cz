---
title: Určování pořadí zpráv Azure Service Bus a časová razítka | Dokumentace Microsoftu
description: Zachování pořadí zpráv služby Service Bus a pořadí s časovými razítky
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
ms.openlocfilehash: 8665d0a1fccecf5521a553a894e2a55e52384ec3
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54851381"
---
# <a name="message-sequencing-and-timestamps"></a>Určování pořadí zpráv a časová razítka

Informace o sekvencování a časového razítka jsou dvě funkce, které jsou vždy povolena na všechny entity služby Service Bus a objevit až [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) a [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) vlastností přijatý nebo Prohlédnuté zprávy.

Pro případy, ve kterých je důležité absolutní pořadí zpráv a/nebo ve kterém spotřebitel musí důvěryhodného jedinečný identifikátor zpráv, zpráv razítka zprostředkovatele s gap, bez zvýšení pořadové číslo vzhledem k fronty nebo tématu. Pro dělené entity je vydána pořadové číslo vzhledem k oddílu.

**SequenceNumber** hodnota je jedinečné celé 64-bit přiřazená zpráva přijata a uložena jako jeho interní identifikátor zprostředkovatele a funkcemi. Pro dělené entity nejvyšší 16 bitů, aby odrážely identifikátor oddílu. Pořadová čísla nespotřebujete na hodnotu nula, když se vyčerpá rozsah 48 a 64-bit.

Pořadové číslo může být důvěryhodné jako jedinečný identifikátor, protože je přiřazena autoritou – střed a neutrální a ne klienty. Také představuje true pořadí doručení a můžete ji přesnější než časové razítko jako kritérium pořadí, protože časová razítka nemusí mít dostatečné řešení sazby extreme zprávu a se můžou stát terčem (ale minimální) hodiny zkosení v situacích, kdy zprostředkovatel vlastnictví přechází mezi uzly.

Je důležité pořadí doručení absolutní, například v obchodní scénáře, ve kterých omezený počet nabízejí zboží jsou poskytovány na základě přijde first dřív do vyčerpání zásob poslední; Příkladem jsou prodej lístků vzájemné součinnosti.

Funkci časového razítka funguje jako neutrální a důvěryhodné autority, přesně zachycující čas UTC přijetí zprávy v **EnqueuedTimeUtc** vlastnost. Hodnota je užitečná, pokud závisí podnikový scénář na termíny, jako je například určuje, zda pracovní položka byla odeslána na určité datum před půlnocí, ale je daleko za nevyřízené žádosti ve frontě zpracování.

## <a name="scheduled-messages"></a>Plánované zprávy

Můžete odesílat zprávy do fronty nebo tématu ke zpožděním zpracování; Chcete-li například naplánovat úlohu k dispozici pro zpracování na jednom systému v určitém čase. Tato funkce provádí spolehlivé distribuovaný Plánovač podle času.

Plánované zprávy ve frontě není materializovat až do doby definované zařadit do fronty. Před uplynutím této doby se dá zrušit plánované zprávy. Zrušení zprávu odstraní.

Můžete naplánovat zprávy buď tak, že nastavíte [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) při odesílání zprávy pomocí regulárních odchozí cestě, nebo explicitně pomocí [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) rozhraní API. Druhá možnost okamžitě vrátí naplánovanou zprávu **SequenceNumber**, které můžete později zrušit naplánovanou zprávu v případě potřeby. Plánované zprávy a jejich čísla pořadí lze také zjistit pomocí [procházení zpráv](message-browsing.md).

**SequenceNumber** naplánovanou zprávu platí pouze při zprávy je v tomto stavu. Jako přechody zprávu do stavu aktivní, zpráva se připojí k frontě jako kdyby byla zařazených do fronty v okamžiku aktuální, která zahrnuje přiřazení nové **SequenceNumber**.

Protože tato funkce je ukotven na jednotlivé zprávy a zprávy může být pouze zařazených do fronty jednou, Service Bus nepodporuje opakující se plány pro zprávy.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)