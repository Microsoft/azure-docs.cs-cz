---
title: Přehled standardních rozhraní API služby Azure Event Hubs .NET | Dokumenty společnosti Microsoft
description: .NET Standardní rozhraní API – přehled
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60821908"
---
# <a name="event-hubs-net-standard-api-overview"></a>Přehled rozhraní API centra událostí .NET

Tento článek shrnuje některé klíčové [rozhraní API klienta](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)Azure Event Hubs .NET Standard . Pro centra událostí jsou aktuálně k dispozici dvě klientské knihovny .NET Standard:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): Poskytuje všechny základní operace runtime.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): Přidá další funkce, které umožňuje sledování zpracovaných událostí a je nejjednodušší způsob, jak číst z centra událostí.

## <a name="event-hubs-client"></a>Klient Centra událostí

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) je primární objekt, který slouží k odesílání událostí, vytváření příjemců a získání informací za běhu. Tento klient je propojen s určitým centrem událostí a vytvoří nové připojení ke koncovému bodu Centra událostí.

### <a name="create-an-event-hubs-client"></a>Vytvoření klienta pro centra událostí (Event Hubs)

Objekt [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) je vytvořen z připojovacího řetězce. Nejjednodušší způsob vytvoření instance nového klienta je uveden v následujícím příkladu:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Chcete-li programově upravit připojovací řetězec, můžete použít třídu [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) a předat připojovací řetězec jako parametr [eventhubclient.createfromconnectionstring](/dotnet/api/microsoft.azure.eventhubs.eventhubclient).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Odesílání událostí

Chcete-li odeslat události do centra událostí, použijte třídu [EventData.](/dotnet/api/microsoft.azure.eventhubs.eventdata) Tělo musí být `byte` pole nebo `byte` segment pole.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Příjem událostí

Doporučený způsob příjmu událostí z centra událostí používá [hostitele procesoru událostí](#event-processor-host-apis), který poskytuje funkce pro automatické sledování odsazení centra událostí a informací o oddílech. Existují však určité situace, ve kterých můžete chtít použít flexibilitu základní knihovny Event Hubs pro příjem událostí.

#### <a name="create-a-receiver"></a>Vytvoření přijímače

Příjemci jsou vázány na konkrétní oddíly, takže chcete-li přijímat všechny události v centru událostí, musíte vytvořit více instancí. Je vhodné získat informace o oddílu programově, nikoli pevně kódovat ID oddílů. Chcete-li tak učinit, můžete použít [Metodu GetRuntimeInformationAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)

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

Vzhledem k tomu, že události nejsou nikdy odebrány z centra událostí (a vyprší pouze jejich platnost), je nutné zadat správný výchozí bod. Následující příklad ukazuje možné kombinace:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Využití události

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

## <a name="event-processor-host-apis"></a>Hostitelská api hostitele procesoru událostí

Tato rozhraní API poskytují odolnost pracovních procesů, které mohou být nedostupné, distribucí oddílů mezi dostupné pracovníky:

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

Následuje ukázková implementace rozhraní [IEventProcessor:](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)

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

## <a name="next-steps"></a>Další kroky

Další informace o scénářích služby Event Hubs naleznete pod těmito odkazy:

* [Co je služba Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Apis centra událostí k dispozici](event-hubs-api-overview.md)

Odkazy na rozhraní .NET API jsou zde:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
