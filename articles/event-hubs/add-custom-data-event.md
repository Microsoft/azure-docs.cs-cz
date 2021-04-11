---
title: Přidání vlastních dat do událostí v Azure Event Hubs
description: V tomto článku se dozvíte, jak přidat vlastní data do událostí v Azure Event Hubs.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104893468"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Přidání vlastních dat do událostí v Azure Event Hubs
Vzhledem k tomu, že událost se skládá hlavně z neprůhledné sady bajtů, může být pro spotřebitele těchto událostí obtížné se rozhodnout, jak je zpracovat. Pokud chcete, aby vydavatelé událostí nabízeli lepší kontext pro příjemce, můžou události obsahovat také vlastní metadata ve formě sady párů klíč-hodnota. Jedním z běžných scénářů pro zahrnutí metadat je poskytnout nápovědu k typu dat obsažených v události, aby spotřebitelé pochopili svůj formát a mohli je podle potřeby deserializovat.

> [!NOTE]
> Tato metadata nejsou používána nástrojem nebo jakýmkoli způsobem smysluplným pro službu Event Hubs. Existuje pouze pro koordinaci mezi vydavateli událostí a spotřebiteli. 

Následující části ukazují, jak přidat vlastní data do událostí v různých programovacích jazycích. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

Ukázku kompletního kódu najdete v tématu [publikování událostí s vlastními metadaty](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata).

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

Ukázku kompletního kódu najdete v tématu [publikování událostí s vlastními metadaty](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java).


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

Ukázku kompletního kódu najdete v tématu [Odeslání datové dávky událostí pomocí vlastností](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>Další kroky
Podívejte se na následující rychlé starty a ukázky. 

- Rychlé starty: [.NET](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)
- Ukázky na GitHubu: [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples), [Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples), [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript), [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
