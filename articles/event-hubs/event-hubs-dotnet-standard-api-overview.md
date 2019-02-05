---
title: Přehled služby Azure Event Hubs standardní rozhraní API .NET | Dokumentace Microsoftu
description: Přehled standardní rozhraní API .NET
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728415"
---
# <a name="event-hubs-net-standard-api-overview"></a>Přehled služby Event Hubs .NET API úrovně Standard

Tento článek obsahuje souhrn některých klíče služby Azure Event Hubs [.NET Standard klientských rozhraní API](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/). Aktuálně existují dva klientské knihovny .NET Standard pro službu Event Hubs:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): Obsahuje všechny operace základního modulu runtime.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): Přidává další funkce, která umožňuje udržování přehledu o zpracování událostí a je nejjednodušší způsob, jak číst z centra událostí.

## <a name="event-hubs-client"></a>Event Hubs klienta

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) je primární objekt použijete k odesílání událostí a vytvořte příjemce a k získání běhových informací. Tento klient je propojený s konkrétní eventhub a vytvoří nové připojení ke koncovému bodu služby Event Hubs.

### <a name="create-an-event-hubs-client"></a>Vytvoření klienta pro centra událostí (Event Hubs)

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) z připojovacího řetězce je vytvořen objekt. Nejjednodušší způsob, jak vytvořit instanci nového klienta můžete vidět v následujícím příkladu:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Chcete-li programově upravit připojovací řetězec, můžete použít [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) třídy a předat jako parametr pro připojovací řetězec [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Odesílání událostí

K odesílání událostí do centra událostí, použijte [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) třídy. Obsah musí být `byte` pole, nebo `byte` segmentu pole.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Příjem událostí

Doporučený způsob přijímání události ze služby Event Hubs využívá [Event Processor Host](#event-processor-host-apis), která poskytuje funkce, které automaticky udržovat přehled o informace posun a oddílu centra událostí. Existují však některé situace, ve kterých můžete chtít využít flexibilitu základní knihovny Event Hubs přijímat události.

#### <a name="create-a-receiver"></a>Vytvoření příjemce

Příjemci jsou vázané na konkrétní oddíly, tak aby bylo možné přijímat všechny události v Centru událostí, je třeba vytvořit více instancí. Je vhodné získat informace o oddílu prostřednictvím kódu programu, místo pevného kódování ID oddílů. Pokud chcete udělat, můžete použít [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) metody.

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Protože události se nikdy odeberou z centra událostí (a pouze platnost), je nutné zadat správné výchozí bod. Následující příklad zobrazuje možné kombinace:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Zpracovat událost

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>Rozhraní API hostitel procesoru událostí

Tato rozhraní API poskytuje odolnost vůči pracovních procesů, které se můžou stát nedostupnými, díky distribuci oddílů napříč dostupné pracovní procesy:

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Tady je ukázková implementace [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) rozhraní:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>Další postup

Další informace o scénářích služby Event Hubs naleznete pod těmito odkazy:

* [Co je Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Rozhraní API k dispozici události rozbočovače](event-hubs-api-overview.md)

Tady jsou odkazy na rozhraní .NET API:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
