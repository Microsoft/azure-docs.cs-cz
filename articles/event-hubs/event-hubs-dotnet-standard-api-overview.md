---
title: Přehled služby Azure Event Hubs standardní rozhraní API technologie .NET | Microsoft Docs
description: Přehled standardní rozhraní API .NET
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: a173f8e4-556c-42b8-b856-838189f7e636
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 855f6e7f401621d7f923d68215ca880c05d38629
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26782995"
---
# <a name="event-hubs-net-standard-api-overview"></a>Přehled služby Event Hubs .NET standardní API

Tento článek shrnuje některé klíče rozhraní API Standard .NET události rozbočovače klienta. Aktuálně existují dvě rozhraní .NET standardní knihovny klienta:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): poskytuje všechny operace základní runtime.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): přidává další funkce, která umožňuje udržování přehledu o zpracování událostí a je nejjednodušší způsob, jak číst z centra událostí.

## <a name="event-hubs-client"></a>Události rozbočovače klienta

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) je primární objekt, který používáte pro odeslání události vytvořit příjemci a získat informace o běhu. Tento klient je propojena k rozbočovači určitá událost a vytvoří nové připojení ke koncovému bodu služby Event Hubs.

### <a name="create-an-event-hubs-client"></a>Vytvoření klienta pro centra událostí (Event Hubs)

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) z připojovacího řetězce je vytvořen objekt. Nejjednodušší způsob, jak vytvořit nový klient je znázorněno v následujícím příkladu:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("{Event Hubs connection string}");
```

Chcete-li prostřednictvím kódu programu upravit připojovací řetězec, můžete použít [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) třídy a předejte připojovací řetězec jako parametr, který se [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_CreateFromConnectionString_System_String_).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("{Event Hubs connection string}")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Odesílání událostí

Chcete-li odesílání událostí do centra událostí, použijte [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) třídy. Obsah musí být `byte` pole, nebo `byte` segmentu pole.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Příjem událostí

Doporučený způsob přijímání události ze služby Event Hubs je pomocí [Event Processor Host](#event-processor-host-apis), která poskytuje funkce automaticky specifické pro určitou událost posun a oddíl informace o rozbočovači. Existují však určité situace, ve kterých můžete chtít použít možnost základní knihovny služby Event Hubs přijímat události.

#### <a name="create-a-receiver"></a>Vytvoření příjemce

Příjemci, jsou svázané s konkrétní oddíly, tak aby bylo možné přijímat všechny události v Centru událostí, je třeba vytvořit více instancí. Je dobrým zvykem získat informace o oddílu programově, místo pevně kódováno ID oddílu. Chcete-li to provést, můžete použít [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_GetRuntimeInformationAsync) metoda.

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

Protože události jsou nikdy z centra událostí (a pouze vyprší platnost), je nutné zadat správné počáteční bod. Následující příklad ukazuje možné kombinace:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Využívat událost

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

## <a name="event-processor-host-apis"></a>Rozhraní API hostitele procesor událostí

Tato rozhraní API poskytují odolnost pro pracovní procesy, které mohly být nedostupné, a to distribucí oddíly mezi dostupné pracovních procesů.

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

Tady je příklad implementace [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor).

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

* [Co je Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Rozhraní API k dispozici události rozbočovače](event-hubs-api-overview.md)

Odkazy na rozhraní API .NET jsou tady:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)