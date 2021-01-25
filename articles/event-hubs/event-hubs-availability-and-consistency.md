---
title: Dostupnost a konzistence – Azure Event Hubs | Microsoft Docs
description: Jak zajistit maximální možnou dostupnost a konzistenci s Azure Event Hubs pomocí oddílů.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 5ffa2df992eb0c22aafbbb7436250405998d8073
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762812"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Dostupnost a konzistence ve službě Event Hubs

## <a name="overview"></a>Přehled
Azure Event Hubs využívá [model dělení](event-hubs-scalability.md#partitions) ke zlepšení dostupnosti a paralelismu v rámci jednoho centra událostí. Pokud má centrum událostí například čtyři oddíly a jeden z těchto oddílů je přesunut z jednoho serveru na jiný v operaci vyrovnávání zatížení, můžete i nadále odesílat a přijímat tři další oddíly. Více oddílů vám navíc umožní mít více souběžných čtenářů zpracovávajících vaše data, což zlepšuje agregovanou propustnost. Nepostradatelným aspektům návrhu řešení je porozumění dopadům rozdělení do oddílů a řazení v distribuovaném systému.

Pokud chcete vysvětlit kompromis mezi řazením a dostupností, přečtěte si část [Cap věta](https://en.wikipedia.org/wiki/CAP_theorem), která se také označuje jako věta společnosti pivovar. Tento věta popisuje volbu mezi konzistencí, dostupností a tolerancí oddílů. Uvádí, že pro systémy rozdělené podle sítě dochází k vždy kompromisům mezi konzistencí a dostupností.

Věta společnosti pivovar definuje konzistenci a dostupnost následujícím způsobem:
* Tolerance oddílu: schopnost systému zpracování dat pokračovat ve zpracování dat, i když dojde k selhání oddílu.
* Dostupnost: uzel bez selhání vrací rozumnou odpověď v rozumné době (bez chyb nebo časových limitů).
* Konzistence: pro čtení je zaručeno, že pro daného klienta vrátí nejaktuálnější zápis.

> [!NOTE]
> Pojem **partition** se používá v různých kontextech v Event HUBS a Cap věta. 
> - **Event Hubs** uspořádá události do jednoho nebo více oddílů. Oddíly jsou nezávislé a obsahují vlastní posloupnosti dat, která se často rozšiřují různými sazbami. Další informace najdete v tématu [oddíly](event-hubs-features.md#partitions).
> - V **Cap věta** je oddíl komunikační přerušení v rámci mezi uzly v distribuovaném systému.

## <a name="partition-tolerance"></a>Tolerance oddílu
Event Hubs je postaven na základě děleného datového modelu. Během instalace můžete nakonfigurovat počet oddílů v centru událostí, ale tuto hodnotu nemůžete později změnit. Vzhledem k tomu, že je nutné použít oddíly s Event Hubs, musíte učinit rozhodnutí o dostupnosti a konzistenci pro vaši aplikaci.

## <a name="availability"></a>Dostupnost
Nejjednodušší způsob, jak začít s Event Hubs, je použít výchozí chování. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 nebo novější)](#tab/latest)
Pokud vytvoříte nový objekt **[EventHubProducerClient](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient)** a použijete metodu **[SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync)** , události se automaticky rozdělují mezi oddíly v centru událostí. Toto chování umožňuje pro největší dobu provozu.

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 nebo starší)](#tab/old)
Pokud vytvoříte nový objekt **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** a použijete metodu **[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** , vaše události se automaticky rozdělují mezi oddíly v centru událostí. Toto chování umožňuje pro největší dobu provozu.

---

Pro případy použití, které vyžadují maximální dobu provozu, je tento model upřednostňován.

## <a name="consistency"></a>Konzistence
V některých scénářích může být řazení událostí důležité. Například může být vhodné, aby váš back-end systém zpracovával příkaz Update před příkazem DELETE. V této instanci můžete buď nastavit klíč oddílu na událost, nebo použít `PartitionSender` objekt (Pokud používáte starou knihovnu Microsoft. Azure. Messaging) k posílání událostí jenom na určitý oddíl. Tím zajistíte, že při čtení těchto událostí z oddílu budou čteny v daném pořadí. 

Pokud používáte novější knihovnu **Azure. Messaging. EventHubs** , přečtěte si téma [migrace kódu z PartitionSender do EventHubProducerClient pro publikování událostí do oddílu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 nebo novější)](#tab/latest)

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

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 nebo starší)](#tab/old)

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

V případě této konfigurace Pamatujte na to, že pokud konkrétní oddíl, na který odesíláte, není k dispozici, zobrazí se chybová odpověď. Pokud nemáte spřažení k jednomu oddílu, jako bod porovnání, Služba Event Hubs odešle událost do dalšího dostupného oddílu.

Jedním z možných řešení, které zajistí objednávání a zároveň maximalizovat čas, by bylo agregovat události jako součást vaší aplikace pro zpracování událostí. Nejjednodušší způsob, jak to provést, je razítko události pomocí vlastní vlastnosti pořadové číslo. Podívejte se na ukázku kódu:

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 nebo novější)](#tab/latest)

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

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 nebo starší)](#tab/old)
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

Tento příklad odešle událost do jednoho z dostupných oddílů v centru událostí a nastaví odpovídající pořadové číslo z vaší aplikace. Toto řešení vyžaduje, aby byl stav uchováván vaší aplikací pro zpracování, ale poskytuje vašim odesílatelům koncový bod, který bude pravděpodobně k dispozici.

## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](./event-hubs-about.md)
* [Vytvoření centra událostí](event-hubs-create.md)
