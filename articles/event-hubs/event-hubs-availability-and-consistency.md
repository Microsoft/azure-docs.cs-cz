---
title: Dostupnost a konzistence – Azure Event Hubs | Microsoft Docs
description: Jak zajistit maximální možnou dostupnost a konzistenci s Azure Event Hubs pomocí oddílů.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882191"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Dostupnost a konzistence ve službě Event Hubs
Tento článek poskytuje informace o dostupnosti a konzistenci, které podporuje Azure Event Hubs. 

## <a name="availability"></a>Dostupnost
Azure Event Hubs šíří riziko závažných selhání jednotlivých počítačů nebo dokonce kompletních stojanů napříč clustery, které v rámci datového centra přesahují více domén selhání. Implementuje transparentní detekci selhání a mechanismy převzetí služeb při selhání tak, aby služba pokračovala v provozu v rámci zajištěných úrovní služeb a obvykle bez znatelného přerušení, když dojde k takovým selháním. Pokud byl vytvořen obor názvů Event Hubs s povolenou možností pro [zóny dostupnosti](../availability-zones/az-overview.md), je riziko výpadku dále rozdělené mezi tři fyzicky oddělená zařízení a služba má dostatek rezerv pro kapacitu, aby se okamžitě vypořádat s kompletní a závažnou ztrátou celého zařízení. Další informace najdete v tématu [Azure Event Hubs – geografická zotavení po havárii](event-hubs-geo-dr.md).

Když klientská aplikace odešle události do centra událostí, události se automaticky rozdělují mezi oddíly v centru událostí. Pokud oddíl z nějakého důvodu není dostupný, události se rozdělují mezi zbývající oddíly. Toto chování umožňuje pro největší dobu provozu. Pro případy použití, které vyžadují maximální dobu provozu, je tento model upřednostňován místo odesílání událostí do konkrétního oddílu. Další informace najdete v tématu [oddíly](event-hubs-scalability.md#partitions).

## <a name="consistency"></a>Konzistence
V některých scénářích může být řazení událostí důležité. Například může být vhodné, aby váš back-end systém zpracovával příkaz Update před příkazem DELETE. V tomto scénáři klientská aplikace odesílá události do konkrétního oddílu tak, aby bylo řazení zachováno. Když aplikace příjemce spotřebovává tyto události z oddílu, čtou se v daném pořadí. 

V případě této konfigurace Pamatujte na to, že pokud konkrétní oddíl, na který odesíláte, není k dispozici, zobrazí se chybová odpověď. Pokud v bodě porovnání nemáte spřažení k jednomu oddílu, Služba Event Hubs odešle událost do dalšího dostupného oddílu.

Jedním z možných řešení, které zajistí objednávání a zároveň maximalizovat čas, by bylo agregovat události jako součást vaší aplikace pro zpracování událostí. Nejjednodušší způsob, jak to provést, je vyznačení události pomocí vlastní vlastnosti pořadové číslo.

V tomto scénáři klient producenta posílá události do některého z dostupných oddílů v centru událostí a nastaví odpovídající pořadové číslo z vaší aplikace. Toto řešení vyžaduje, aby byl stav uchováván vaší aplikací pro zpracování, ale poskytuje vašim odesílatelům koncový bod, který bude pravděpodobně k dispozici.

## <a name="appendix"></a>Přílohy

### <a name="net-examples"></a>Příklady .NET

#### <a name="send-events-to-a-specific-partition"></a>Odeslání událostí do konkrétního oddílu
Buď nastavte klíč oddílu na událost, nebo použijte `PartitionSender` objekt (Pokud používáte starou knihovnu Microsoft. Azure. Messaging) k odeslání událostí do určitého oddílu. Tím zajistíte, že při čtení těchto událostí z oddílu budou čteny v daném pořadí. 

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

### <a name="set-a-sequence-number"></a>Nastavit pořadové číslo
Následující příklad označí událost pomocí vlastní vlastnosti pořadové číslo. 

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
