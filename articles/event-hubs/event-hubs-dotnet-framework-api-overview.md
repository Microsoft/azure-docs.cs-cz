---
title: Přehled rozhraní API rozhraní API rozhraní Azure Event Hubs .NET Framework | Dokumenty společnosti Microsoft
description: Tento článek obsahuje souhrn některých klíčových rozhraní API klienta rozhraní API rozhraní API rozhraní API rozhraní .NET Framework rozhraní .NET Framework (správa a doba runtime).
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: b14759ed39037bfa172366a2ed8f8ca089786ec6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137607"
---
# <a name="event-hubs-net-framework-api-overview"></a>Přehled rozhraní API rozhraní API rozhraní Event Hubs .NET Framework

Tento článek shrnuje některé klíčové [rozhraní API klienta](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)Azure Event Hubs .NET Framework . Existují dvě kategorie: správa a běhových api. Úložiště API za běhu se skládají ze všech operací potřebných k odeslání a přijetí zprávy. Operace správy umožňují spravovat stav entity Centra událostí vytvořením, aktualizací a odstraněním entit.

[Scénáře monitorování](event-hubs-metrics-azure-monitor.md) se rozprostírají jak ve správě, tak za běhu. Podrobnou referenční dokumentaci k rozhraní MAVAleS .NET naleznete v odkazech rozhraní API [rozhraní .NET Framework](/dotnet/api/microsoft.servicebus.messaging.eventhubclient), [.NET Standard](/dotnet/api/microsoft.azure.eventhubs)a [EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor)

## <a name="management-apis"></a>Rozhraní API pro správu

Chcete-li provádět následující operace správy, musíte mít oprávnění **ke správě** v oboru názvů Centra událostí:

### <a name="create"></a>Vytvořit

```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>Aktualizace

```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Odstranění

```csharp
await namespaceManager.DeleteEventHubAsync("event hub name");
```

## <a name="run-time-apis"></a>Api s run-time
### <a name="create-publisher"></a>Vytvořit vydavatele

```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("event hub name");
```

### <a name="publish-message"></a>Publikovat zprávu

```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Vytvořit spotřebitele

```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Spotřebovávat zprávu

```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Hostitelská api hostitele procesoru událostí

Tato rozhraní API poskytují odolnost proti chybám pracovních procesů, které mohou být nedostupné, distribucí oddílů mezi dostupné pracovníky.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

Rozhraní [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) je definováno takto:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

Další informace o scénářích služby Event Hubs naleznete pod těmito odkazy:

* [Co je služba Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md)

Odkazy na rozhraní .NET API jsou zde:

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft.Azure.EventHubs.EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)
