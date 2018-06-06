---
title: Dostupnost a konzistence v Azure Event Hubs | Microsoft Docs
description: Jak poskytnout maximální množství dostupnosti a konzistence s Azure Event Hubs pomocí oddíly.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: e119406292ca1d805f831bc65e3ae6e583147c6d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700685"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Dostupnost a konzistence v Event Hubs

## <a name="overview"></a>Přehled
Používá Azure Event Hubs [dělení modelu](event-hubs-features.md#partitions) ke zlepšení dostupnosti a paralelního zpracování v rozbočovači jedna událost. Například pokud centra událostí má čtyři oddíly, a jeden z těchto oddílů je přesouvat z jednoho serveru na jiný v operaci Vyrovnávání zatížení, můžete i nadále odesílat a přijímat z tři oddíly. Kromě toho s více oddíly umožňuje mít víc souběžných čtenářů zpracování dat, vylepšení agregovanou propustnost. Vysvětlení důsledků vytváření oddílů a řazení v distribuované systému je zásadní aspekt návrhu řešení.

Vysvětlení kompromis mezi řazení a dostupnosti, najdete v článku [věta CAP](https://en.wikipedia.org/wiki/CAP_theorem), označované také jako věta Brewer společnosti. Tato věta popisuje volba mezi konzistence, dostupnost a odolnost proti oddílu. Uvádí, pro systémy rozdělena na oddíly sítě se vždy kompromis mezi konzistencí a dostupnosti.

Na Brewer věta definuje konzistence a dostupnost následujícím způsobem:
* Oddílu tolerance: možnost zpracování dat systému pokračovat zpracování dat i v případě, že dojde k selhání oddílu.
* Dostupnost: bez selhání uzlu vrátí přiměřené odpověď během přiměřeně krátké doby (bez chyb nebo vypršení časových limitů).
* Konzistence: čtení záruku, že vrátit poslední zápis pro daného klienta.

## <a name="partition-tolerance"></a>Tolerance oddílu
Služba Event Hubs je postavená na oddílů datový model. Počet oddílů v Centru událostí můžete nakonfigurovat během instalace, ale tuto hodnotu nelze změnit později. Vzhledem k tomu, že oddíly musí používat službou Event Hubs, máte k provedení rozhodnutí o dostupnosti a konzistence pro vaši aplikaci.

## <a name="availability"></a>Dostupnost
Nejjednodušší způsob, jak začít pracovat s Event Hubs je použije výchozí chování. Pokud vytvoříte novou **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** objektu a použít **[odeslat](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** metody událostí je automaticky distribuovaná mezi oddíly v Centru událostí. Toto chování umožňuje největší množství provoz.

Tento model pro případy použití, které vyžadují maximální doba provozu, je upřednostňovaný.

## <a name="consistency"></a>Konzistence
V některých případech může být řazení událostí důležité. Například můžete systému back-end a zpracovat příkaz aktualizace před příkaz delete. V tomto případě můžete buď nastavit klíč oddílu na události, nebo použijte `PartitionSender` objekt jenom k odesílání událostí k určité oddílu. Tím zajistíte, že když tyto události se načítají z oddílu, jejich přečtení v pořadí.

Pomocí této konfigurace Uvědomte si, že konkrétní oddíl, do které odesíláte, není k dispozici, obdržíte chybnou odpověď. Jako bod porovnání Pokud nemáte spřažení do jediného oddílu služby Event Hubs odešle událost další dostupný oddíl.

Jedním z možných řešení zajistit řazení při také maximalizace provoz, bude pro agregaci událostí v rámci událost zpracování aplikace. Nejjednodušší způsob, jak tomu je razítka událost s vlastnost počtu vlastní pořadí. Příklad ukazuje následující kód:

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

Tento příklad odešle událost na jednu z dostupných oddílů v Centru událostí a nastaví odpovídající pořadové číslo z vaší aplikace. Toto řešení vyžaduje stavu budou muset zůstat aplikací zpracování, ale dává vaší odesílatelé koncový bod, který může být k dispozici.

## <a name="next-steps"></a>Další postup
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
