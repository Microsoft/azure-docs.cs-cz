---
title: Použití úložiště objektů blob jako úložiště kontrolních bodů v centru Azure Stack Hub (preview)
description: Tento článek popisuje, jak používat úložiště objektů blob jako úložiště kontrolních bodů v rozbočovačích událostí v centru Azure Stack Hub (preview).
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 1f0e4dea44007ef82cb4b700ff0be4a5579541d8
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398919"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Použití úložiště objektů blob jako úložiště kontrolních bodů – centra událostí v centru Azure Stack Hub (preview)
Pokud používáte Azure Blob Storage jako úložiště kontrolních bodů v prostředí, které podporuje jinou verzi sady Storage Blob SDK než ty, které jsou obvykle dostupné v Azure, budete muset použít kód ke změně verze rozhraní API služby úložiště na konkrétní verzi podporovanou tímto prostředím. Například pokud používáte [Centra událostí na Azure Stack Hub verze 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), nejvyšší dostupná verze pro službu Storage je verze 2017-11-09. V takovém případě musíte použít kód k cílení verze rozhraní API služby úložiště na 2017-11-09. Příklad, jak cílit na konkrétní verzi rozhraní API úložiště, najdete v těchto ukázkách na GitHubu: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) nebo [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts), 
- Python - [synchronní](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/event_processor_blob_storage_example_with_storage_api_version.py), [asynchronní](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

> [!IMPORTANT]
> Centra událostí v centru Azure Stack Hub je momentálně ve [verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) a je zdarma. 

Pokud spustíte přijímač centra událostí, který používá úložiště objektů Blob jako úložiště kontrolních bodů bez cílení na verzi, kterou azure zásobníku podporuje, zobrazí se následující chybová zpráva:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Ukázková chybová zpráva v Pythonu
V Pythonu `azure.core.exceptions.HttpResponseError` je chyba předána `on_error(partition_context, error)` obslužné rutině chyby aplikace `EventHubConsumerClient.receive()`. Ale metoda `receive()` nevyvolá výjimku. `print(error)`vytiskne následující informace o výjimce:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

Protokolování bude protokolovat dvě upozornění, jako jsou následující:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Další kroky

Další informace o dělení a vytváření kontrolních bodů: [Vyvážení zatížení oddílů mezi více instancemi aplikace](event-processor-balance-partition-load.md)
