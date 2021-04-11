---
title: Správa koncových bodů streamování
description: Tento článek ukazuje, jak spravovat koncové body streamování pomocí Azure Media Services V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 5ad7ef447e1b8e5ac141e4feb65f1b8361b2a646
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281933"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Správa koncových bodů streamování pomocí Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Po vytvoření účtu Media Services se do vašeho účtu přidá **výchozí** [koncový bod streamování](stream-streaming-endpoint-concept.md) ve stavu **Zastaveno** . Pokud chcete spustit streamování vašeho obsahu a využít výhod [dynamického balení](encode-dynamic-packaging-concept.md) a [dynamického šifrování](drm-content-protection-concept.md), musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **spuštěno** .

V tomto článku se dozvíte, jak spustit příkaz [Start](/rest/api/media/streamingendpoints/start) na koncovém bodu streamování pomocí různých technologií. 
 
> [!NOTE]
> Fakturuje se vám jenom v případě, že je koncový bod streamování ve stavu spuštěno.
    
## <a name="prerequisites"></a>Požadavky

Zrevidujte 

* [Media Services koncepty](concepts-overview.md)
* [Koncept koncového bodu streamování](stream-streaming-endpoint-concept.md)
* [Dynamické balení](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>Použití REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Další informace naleznete v tématu: 

* Referenční dokumentaci ke [spuštění StreamingEndpoint](/rest/api/media/streamingendpoints/start) .
* Spuštění koncového bodu streamování je asynchronní operace. 

    Informace o tom, jak monitorovat dlouhotrvající operace, najdete v tématu [dlouhotrvající operace](media-services-apis-overview.md).
* Tato [kolekce pro publikování](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) obsahuje příklady více operací REST, včetně postupu spuštění koncového bodu streamování.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal 
 
1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Přejít na účet Azure Media Services.
1. V levém podokně vyberte  **koncové body streamování**.
1. Vyberte koncový bod streamování, který chcete spustit, a pak vyberte **Spustit**.

## <a name="use-the-azure-cli"></a>Použití Azure CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Další informace najdete v tématu [AZ AMS streaming-Endpoint Start](/cli/azure/ams/streaming-endpoint#az-ams-streaming-endpoint-start).

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

Podívejte se na kompletní [ukázku kódu .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Další kroky

* [Media Services V3 OpenAPI Specification (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Streamování operací koncového bodu](/rest/api/media/streamingendpoints)
