---
title: Správa koncových bodů streamování pomocí Azure Media Services v3
description: Tento článek ukazuje, jak spravovat koncové body streamování pomocí Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461040"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Správa koncových bodů streamování pomocí služby Media Services v3

Při vytvoření účtu Media Services je **k** vašemu účtu ve stavu **Zastaveno** přidán výchozí [koncový bod streamování.](streaming-endpoint-concept.md) Chcete-li spustit streamování obsahu a využít [výhod dynamického balení](dynamic-packaging-overview.md) a [dynamického šifrování](content-protection-overview.md), musí být koncový bod streamování, ze kterého chcete streamovat obsah, ve stavu **Spuštěno.**

Tento článek ukazuje, jak spustit příkaz [start](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) na koncovém bodu streamování pomocí různých technologií. 
 
> [!NOTE]
> Bude se vám účtovat pouze v případě, že je koncový bod streamování ve spuštěném stavu.
    
## <a name="prerequisites"></a>Požadavky

Recenzi: 

* [Koncepty mediálních služeb](concepts-overview.md)
* [Koncept koncového bodu streamování](streaming-endpoint-concept.md)
* [Dynamické balení](dynamic-packaging-overview.md)

## <a name="use-rest"></a>Použití REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Další informace naleznete v tématu: 

* Spuštění referenční dokumentace [StreamingEndpoint.](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* Spuštění koncového bodu streamování je asynchronní operace. 

    Informace o monitorování dlouhotrvajících operací naleznete v [tématu Dlouhotrvající operace](media-services-apis-overview.md).
* Tato [kolekce Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) obsahuje příklady více operací REST, včetně o tom, jak spustit koncový bod streamování.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal 
 
1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Přejděte na svůj účet Azure Media Services.
1. V levém podokně vyberte **Položku Koncové body streamování**.
1. Vyberte koncový bod streamování, který chcete spustit, a pak vyberte **Start**.

## <a name="use-the-azure-cli"></a>Použití Azure CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Další informace naleznete [v tématu az ams streaming-endpoint start](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-sdks"></a>Použití sad SDK

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Podívejte se na kompletní [ukázku kódu Java](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Podívejte se na ukázku úplného [kódu .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Další kroky

* [Mediální služby v3 OpenAPI Specifikace (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Operace koncového bodu streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints)
