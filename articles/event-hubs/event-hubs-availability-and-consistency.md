---
title: Dostupnost a konzistence – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Jak poskytnout maximální dostupnost a konzistenci s Azure Event Hubs pomocí oddílů.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2020
ms.author: shvija
ms.openlocfilehash: 0546adb6131479a8f5d2e7e31819483200586839
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397334"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Dostupnost a konzistence ve službě Event Hubs

## <a name="overview"></a>Přehled
Azure Event Hubs používá [model dělení](event-hubs-scalability.md#partitions) ke zlepšení dostupnosti a paralelizace v rámci jednoho centra událostí. Například pokud rozbočovač událostí má čtyři oddíly a jeden z těchto oddílů se přesune z jednoho serveru na jiný v operaci vyrovnávání zatížení, můžete stále odesílat a přijímat ze tří dalších oddílů. Navíc s více oddílů umožňuje mít více souběžných čteček zpracování dat, zlepšení agregační propustnost. Pochopení důsledků dělení a řazení v distribuovaném systému je kritickým aspektem návrhu řešení.

Chcete-li vysvětlit kompromis mezi objednáváním a dostupností, podívejte se na [teorém SZP](https://en.wikipedia.org/wiki/CAP_theorem), známý také jako Brewerova teorém. Tato teoreta popisuje volbu mezi konzistence, dostupnost a tolerance oddílu. Uvádí, že pro systémy rozdělené podle sítě je vždy kompromis mezi konzistence a dostupnost.

Brewerova teorém definuje konzistenci a dostupnost takto:
* Tolerance oddílu: schopnost systému zpracování dat pokračovat ve zpracování dat i v případě, že dojde k selhání oddílu.
* Dostupnost: uzel bez selhání vrátí přiměřenou odpověď v přiměřené době (bez chyb nebo časového období).
* Konzistence: čtení je zaručeno, že vrátí nejnovější zápis pro daného klienta.

## <a name="partition-tolerance"></a>Tolerance oddílu
Centra událostí je postavena na nad oddíly datového modelu. Počet oddílů v centru událostí můžete během instalace nakonfigurovat, ale tuto hodnotu nelze později změnit. Vzhledem k tomu, že je nutné použít oddíly s Event Hubs, musíte se rozhodnout o dostupnosti a konzistenci pro vaši aplikaci.

## <a name="availability"></a>Dostupnost
Nejjednodušší způsob, jak začít s Event Hubs je použít výchozí chování. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 nebo novější)](#tab/latest)
Pokud vytvoříte nový objekt **[EventHubProducerClient](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient?view=azure-dotnet)** a použijete metodu **[SendAsync,](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync?view=azure-dotnet)** vaše události se automaticky distribuují mezi oddíly v centru událostí. Toto chování umožňuje největší množství času nahoru.

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 nebo starší)](#tab/old)
Pokud vytvoříte nový objekt **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** a použijete metodu **[Send,](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** vaše události se automaticky distribuují mezi oddíly v centru událostí. Toto chování umožňuje největší množství času nahoru.

---

Pro případy použití, které vyžadují maximální dobu provozu, je tento model upřednostňován.

## <a name="consistency"></a>Konzistence
V některých scénářích může být důležité pořadí událostí. Můžete například chtít, aby systém back-end zpracoval příkaz aktualizace před příkazem delete. V tomto případě můžete buď nastavit klíč oddílu na `PartitionSender` událost, nebo použít objekt (pokud používáte starou knihovnu Microsoft.Azure.Messaging) pouze k odesílání událostí do určitého oddílu. Tím zajistíte, že při čtení těchto událostí z oddílu jsou čteny v pořadí. Pokud používáte knihovnu **Azure.Messaging.EventHubs** a další informace, přečtěte si další informace, přečtěte si další informace o [migraci kódu z PartitionSender na EventHubProducerClient pro publikování událostí do oddílu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 nebo novější)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 nebo starší)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

Při této konfiguraci mějte na paměti, že pokud konkrétní oddíl, do kterého odesíláte, není k dispozici, obdržíte odpověď na chybu. Pro srovnání, pokud nemáte spřažení s jedním oddílem, služba Event Hubs odešle událost do dalšího dostupného oddílu.

Jedním z možných řešení pro zajištění řazení a zároveň maximalizace doby využití by bylo agregovat události jako součást aplikace pro zpracování událostí. Nejjednodušší způsob, jak toho dosáhnout, je razítko události s vlastní vlastnost pořadové číslo. Příklad ukazuje následující kód:

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 nebo novější)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 nebo starší)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

Tento příklad odešle událost do jednoho z dostupných oddílů v centru událostí a nastaví odpovídající pořadové číslo z vaší aplikace. Toto řešení vyžaduje stav, který má být zachován vaší aplikací zpracování, ale dává odesílatelům koncový bod, který je pravděpodobnější, že bude k dispozici.

## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
