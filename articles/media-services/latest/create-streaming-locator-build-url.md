---
title: Vytvoření lokátoru streamování a adres URL sestavení – Azure Media Services
description: Tento článek ukazuje, jak vytvořit Lokátor streamování a adresy URL sestavení.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 916defd652ff9f4b3f372c609055d2a974126916
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295712"
---
# <a name="create-a-streaming-locator-and-build-urls"></a>Vytvoření lokátoru streamování a adres URL

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pokud chcete v Azure Media Services vytvořit adresu URL streamování, musíte nejdřív vytvořit [Lokátor streamování](streaming-locators-concept.md). Potom zřetězete název hostitele [koncového bodu streamování](/rest/api/media/streamingendpoints) a cestu k **lokátoru streamování** . V této ukázce se používá *výchozí* **koncový bod streamování** . Když poprvé vytvoříte účet služby Media Service, bude tento *výchozí* **koncový bod streamování** v zastaveném stavu, takže musíte zavolat **Spustit** , aby se spustilo streamování.

Tento článek ukazuje, jak vytvořit Lokátor streamování a vytvořit adresu URL streamování pomocí sad Java a .NET SDK.

## <a name="prerequisite"></a>Požadavek 

Náhled [dynamického balení](dynamic-packaging-overview.md)

## <a name="java"></a>Java

```java
/**
* Creates a StreamingLocator for the specified asset and with the specified streaming policy name.
* Once the StreamingLocator is created the output asset is available to clients for playback.
* @param manager       The entry point of Azure Media resource management
* @param resourceGroup The name of the resource group within the Azure subscription
* @param accountName   The Media Services account name
* @param assetName     The name of the output asset
* @param locatorName   The StreamingLocator name (unique in this case)
* @return              The locator created
*/
private static StreamingLocator getStreamingLocator(MediaManager manager, String resourceGroup, String accountName,
    String assetName, String locatorName) {
    // Note that we are using one of the PredefinedStreamingPolicies which tell the Origin component
    // of Azure Media Services how to publish the content for streaming.
    System.out.println("Creating a streaming locator...");
    StreamingLocator locator = manager
        .streamingLocators().define(locatorName)
        .withExistingMediaservice(resourceGroup, accountName)
        .withAssetName(assetName)
        .withStreamingPolicyName("Predefined_ClearStreamingOnly")
        .create();

    return locator;
}

/**
* Checks if the streaming endpoint is in the running state, if not, starts it.
* @param manager       The entry point of Azure Media resource management
* @param resourceGroup The name of the resource group within the Azure subscription
* @param accountName   The Media Services account name
* @param locatorName   The name of the StreamingLocator that was created
* @param streamingEndpoint     The streaming endpoint.
* @return              List of streaming urls
*/
private static List<String> getStreamingUrls(MediaManager manager, String resourceGroup, String accountName,
    String locatorName, StreamingEndpoint streamingEndpoint) {
    List<String> streamingUrls = new ArrayList<>();

    ListPathsResponse paths = manager.streamingLocators().listPathsAsync(resourceGroup, accountName, locatorName)
        .toBlocking().first();
    
    for (StreamingPath path: paths.streamingPaths()) {
        StringBuilder uriBuilder = new StringBuilder();
        uriBuilder.append("https://")
            .append(streamingEndpoint.hostName())
            .append("/")
            .append(path.paths().get(0));

        streamingUrls.add(uriBuilder.toString());
    }
    return streamingUrls;
}
```

Viz kompletní ukázka kódu: [EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/src/main/java/sample/EncodingWithMESPredefinedPreset.java)

## <a name="net"></a>.NET

```csharp
/// <summary>
/// Creates a StreamingLocator for the specified asset and with the specified streaming policy name.
/// Once the StreamingLocator is created the output asset is available to clients for playback.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="assetName">The name of the output asset.</param>
/// <param name="locatorName">The StreamingLocator name (unique in this case).</param>
/// <returns>A task.</returns>
private static async Task<StreamingLocator> CreateStreamingLocatorAsync(
    IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string assetName,
    string locatorName)
{
    Console.WriteLine("Creating a streaming locator...");
    StreamingLocator locator = await client.StreamingLocators.CreateAsync(
        resourceGroup,
        accountName,
        locatorName,
        new StreamingLocator
        {
            AssetName = assetName,
            StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
        });

    return locator;
}

/// <summary>
/// Checks if the streaming endpoint is in the running state,
/// if not, starts it. Then, builds the streaming URLs.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="locatorName">The name of the StreamingLocator that was created.</param>
/// <param name="streamingEndpoint">The streaming endpoint.</param>
/// <returns>A task.</returns>
private static async Task<IList<string>> GetStreamingUrlsAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    String locatorName,
    StreamingEndpoint streamingEndpoint)
{
    IList<string> streamingUrls = new List<string>();

    ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

    foreach (StreamingPath path in paths.StreamingPaths)
    {
        UriBuilder uriBuilder = new UriBuilder
        {
            Scheme = "https",
            Host = streamingEndpoint.HostName,

            Path = path.Paths[0]
        };
        streamingUrls.Add(uriBuilder.ToString());
    }

    return streamingUrls;
}
```

Viz kompletní ukázka kódu: [EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/Program.cs)

## <a name="see-also"></a>Viz také

* [Vytváření filtrů pomocí .NET](filters-dynamic-manifest-dotnet-howto.md)
* [Vytváření filtrů pomocí REST](filters-dynamic-manifest-rest-howto.md)
* [Vytváření filtrů pomocí rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>Další kroky

[Chraňte svůj obsah pomocí DRM](protect-with-drm.md).
