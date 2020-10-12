---
title: Použití Blob Storage jako úložiště kontrolních bodů v centru Azure Stack (Preview)
description: Tento článek popisuje, jak používat Blob Storage jako úložiště kontrolního bodu v Event Hubs v centru Azure Stack (Preview).
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1c876ed36be6aec9395a1acba3a1deb25a47de2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87039221"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Použití Blob Storage jako kontrolní bod úložiště – Event Hubs v Azure Stackm centru (Preview)
Pokud používáte Azure Blob Storage jako úložiště kontrolního bodu v prostředí, které podporuje jinou verzi sady SDK pro úložiště objektů blob, než jsou ta, která jsou obvykle k dispozici v Azure, budete muset použít kód ke změně verze rozhraní API služby úložiště na konkrétní verzi podporovanou tímto prostředím. Pokud například používáte [Event Hubs v centru Azure Stack, verze 2002](/azure-stack/user/event-hubs-overview), nejvyšší dostupná verze služby úložiště je verze 2017-11-09. V takovém případě je nutné použít kód pro cílení na verzi rozhraní API služby úložiště na 2017-11-09. Příklad cílení na konkrétní verzi rozhraní API úložiště najdete v těchto ukázkách na GitHubu: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) nebo  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python – [synchronní](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [asynchronní](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> Event Hubs v centru Azure Stack je momentálně ve [verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) a je zdarma. 

Pokud spustíte Event Hubs přijímač, který jako úložiště kontrolního bodu používá Blob Storage, aniž by se cílí na verzi, kterou centrum Azure Stack podporuje, zobrazí se tato chybová zpráva:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Ukázková chybová zpráva v Pythonu
V případě Pythonu je chyba `azure.core.exceptions.HttpResponseError` předána obslužné rutině chyby `on_error(partition_context, error)` pro `EventHubConsumerClient.receive()` . Metoda ale `receive()` nevyvolává výjimku. `print(error)` vytisknou se následující informace o výjimce:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

Protokolovací nástroj bude protokolovat dvě upozornění, jako jsou následující:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Další kroky

V následujícím článku se dozvíte o dělení a vytváření kontrolních bodů: [vyvážení zátěže oddílu napříč několika instancemi vaší aplikace](event-processor-balance-partition-load.md)
