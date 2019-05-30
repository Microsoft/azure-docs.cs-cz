---
title: Streamování lokátory ve službě Azure Media Services | Dokumentace Microsoftu
description: Tento článek obsahuje vysvětlení, co jsou lokátory streamování a jak se používají Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299173"
---
# <a name="streaming-locators"></a>Lokátory streamování

Pokud chcete, aby videa ve výstupním prostředku byla k dispozici klientům pro přehrávání, musíte vytvořit [lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators) a adresy URL pro streamování. Sestavit adresu URL, budete muset řetězit název hostitele koncového bodu streamování a cestu Lokátor streamování. Ukázku v .NET najdete v tématu věnovaném [získání lokátoru streamování](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces vytváření **Lokátor streamování** nazývá publikování. Ve výchozím nastavení **Lokátor streamování** platnost okamžitě po provedení volání rozhraní API a trvá, dokud je odstraníme, pokud nenakonfigurujete volitelné počáteční a koncový čas. 

Při vytváření **Lokátor streamování**, je nutné zadat **Asset** název a **streamování zásad** název. Další informace naleznete v následujících tématech:

* [Prostředky](assets-concept.md)
* [Zásady streamování](streaming-policy-concept.md)
* [Zásady symetrických klíčů](content-key-policy-concept.md)

Můžete také zadat počáteční a koncový čas na vašich Lokátor streamování, které vám umožní pouze uživateli přehrávání obsahu mezi tyto časy (například mezi 5/1/2019 na 5/5/2019).  

## <a name="considerations"></a>Požadavky

* **Lokátory streamování** nelze aktualizovat. 
* Vlastnosti **lokátory streamování** jsou DateTime typu jsou vždy ve formátu UTC.
* Navrhněte omezenou sadu zásad pro svůj účet Media Service a je znovu použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti. Další informace najdete v tématu [kvóty a omezení](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Vytvoření lokátory streamování  

### <a name="not-encrypted"></a>Není šifrováno

Pokud chcete ke streamování vašeho souboru v the vymazat (nešifrované), nastavte předdefinované jasné zásady streamování: na "Predefined_ClearStreamingOnly" (v .NET, můžete použít PredefinedStreamingPolicy.ClearStreamingOnly enum).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Šifrované 

Pokud potřebujete k šifrování obsahu pomocí šifrování CENC, nastavte zásadu "Predefined_MultiDrmCencStreaming". Šifrování Widevine uplatní na datový proud DASH a PlayReady hladký. Klíč doručen klientovi přehrávání podle nakonfigurovaného licence DRM.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Pokud chcete zašifrovat HLS datový proud s CBCS (FairPlay), použijte "Predefined_MultiDrmStreaming".

## <a name="associate-filters-with-streaming-locators"></a>Lokátory streamování přidružit filtry

Zobrazit [filtry: přidružili lokátory streamování](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtr, pořadí, stránka Lokátor streamování entity

Zobrazit [filtrování, řazení, stránkování, Media Services entit](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Lokátory streamování seznam podle názvu prostředku

Lokátory streamování podle přidružený název Assetu, použijte následující operace:

|Jazyk|Rozhraní API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|Rozhraní příkazového řádku|[AZ ams asset seznam streaming lokátory](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>Viz také

* [Prostředky](assets-concept.md)
* [Zásady streamování](streaming-policy-concept.md)
* [Zásady symetrických klíčů](content-key-policy-concept.md)

## <a name="next-steps"></a>Další postup

[Kurz: Nahrávání, kódování a streamování videí pomocí .NET](stream-files-tutorial-with-api.md)
