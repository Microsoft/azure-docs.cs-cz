---
title: Dostupnost a konzistence ve službě Azure Event Hubs | Dokumentace Microsoftu
description: Jak zadat maximální dobu, dostupnost a konzistence pomocí Azure Event Hubs pomocí oddíly.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 9b4d992d690bb3237f8c92e44020c0ac83978d7e
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058652"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Dostupnost a konzistence ve službě Event Hubs

## <a name="overview"></a>Přehled
Služba Azure Event Hubs využívá [dělení modelu](event-hubs-features.md#partitions) ke zlepšení dostupnosti a paralelního zpracování v rámci jednoho centra událostí. Například pokud má čtyři oddíly centra událostí a jeden z těchto oddílů je přesunout z jednoho serveru na jiný v operaci Vyrovnávání zatížení, můžete stále odesílat a přijímat z tři další oddíly. Kromě toho s více oddílů vám umožní mít více souběžných čtenářů zpracování dat, zlepšení agregovanou propustnost. Vysvětlení důsledků vytváření oddílů a řazení v distribuovaném systému je důležitý aspekt návrhu řešení.

Pomoci s vysvětlením kompromis mezi řazení a dostupnosti, najdete v článku [věty](https://en.wikipedia.org/wiki/CAP_theorem), označovaný také jako věta Bureš společnosti. Tento článek popisuje tento věta možností volby mezi konzistencí, dostupností a tolerance oddílu. Uvádí, že pro systémy rozdělené podle sítě je vždycky kompromis mezi konzistencí a dostupností.

Bureš společnosti věta definuje konzistenci a dostupnost následujícím způsobem:
* Rozdělit proti chybám: schopnost systému zpracování dat pokračovat zpracování dat i v případě, že dojde k selhání oddílu.
* Dostupnost: bez selhání uzlu vrátí přiměřené odpověď v rozumném čase (pomocí žádné chyby nebo vypršení časového limitu).
* Konzistence: čtení je zaručeno, vraťte poslední zápis pro daného klienta.

## <a name="partition-tolerance"></a>Tolerance oddílu
Event Hubs je nástavbou dělená data modelu. Můžete nakonfigurovat počet oddílů v Centru událostí během instalace, ale tuto hodnotu nemůžete změnit později. Jelikož oddíly musí používat s Event Hubs, budete muset učinit rozhodnutí o dostupnosti a konzistence pro vaši aplikaci.

## <a name="availability"></a>Dostupnost
Nejjednodušší způsob, jak začít pracovat s Event Hubs je použije výchozí chování. Pokud vytvoříte nový **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** objektu a použít **[odeslat](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** metody událostí je automaticky distribuovaná mezi oddíly v Centru událostí. Toto chování umožňuje co největší množství doba provozu.

Pro případy použití, které vyžadují maximální doba provozu tento model je upřednostňována.

## <a name="consistency"></a>Konzistence
V některých případech může být řazení událostí důležité. Například můžete váš back-end systém ke zpracování příkazu k aktualizaci před příkaz pro odstranění. V takovém případě můžete nastavit klíč oddílu na události, nebo použít `PartitionSender` objekt jenom k odesílání událostí do určité oddílu. Tím se zajistí, že při čtení z oddílu tyto události jsou načteny v pořadí.

S touto konfigurací Uvědomte si, že pokud konkrétní oddíl, do kterého odesíláte není k dispozici, zobrazí se chybová odpověď. Jako bod porovnání Pokud nemáte spřažení pro jeden oddíl, službu Event Hubs odesílá události do další dostupný oddíl.

Jedním z možných řešení k zajištění, řazení a také maximalizuje čas, bude pro agregaci událostí v rámci vaší aplikace pro zpracování událostí. Nejjednodušší způsob, jak to provést, je razítku událost s vlastností číslo vlastní pořadí. Příklad ukazuje následující kód:

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

Tento příklad odešle událost k jednomu z dostupných oddílů v Centru událostí a nastaví odpovídající pořadové číslo z vaší aplikace. Toto řešení vyžaduje stav uchovávat ve vaší aplikaci na zpracování, ale dává váš odesílatelů koncový bod, který je pravděpodobně být k dispozici.

## <a name="next-steps"></a>Další postup
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
