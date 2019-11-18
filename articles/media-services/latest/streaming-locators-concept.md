---
title: Lokátory streamování v Azure Media Services | Microsoft Docs
description: Tento článek obsahuje vysvětlení toho, co jsou Lokátory streamování a jak se používají Azure Media Services.
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
ms.openlocfilehash: 6d13ca5b3657f1deac9e6b4218decf8fe57eb1d9
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113755"
---
# <a name="streaming-locators"></a>Lokátory streamování

Pokud chcete, aby videa ve výstupním prostředku byla k dispozici klientům pro přehrávání, musíte vytvořit [lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators) a adresy URL pro streamování. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele koncového bodu streamování a cestu k lokátoru streamování. Ukázku v .NET najdete v tématu věnovaném [získání lokátoru streamování](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces vytvoření **lokátoru streamování** se nazývá publikování. Ve výchozím nastavení je **Lokátor streamování** platný hned po volání rozhraní API a trvá až do odstranění, pokud nenastavíte volitelné počáteční a koncové časy. 

Při vytváření **lokátoru streamování**musíte zadat název **assetu** a název **zásady streamování** . Další informace najdete v následujících tématech:

* [Aktiva](assets-concept.md)
* [Zásady streamování](streaming-policy-concept.md)
* [Zásady symetrických klíčů](content-key-policy-concept.md)

Můžete také zadat čas zahájení a ukončení na lokátoru streamování, který umožní uživateli přehrávat obsah mezi těmito časy (například mezi 5/1/2019 a 5/5/2019).  

## <a name="considerations"></a>Požadavky

* **Lokátory streamování** nejde aktualizovat. 
* Vlastnosti **lokátorů streamování** , které jsou typu DateTime, jsou vždycky ve formátu UTC.
* Měli byste navrhnout omezené sady zásad pro svůj účet Media Service a znovu je použít pro Lokátory streamování, pokud jsou potřeba stejné možnosti. Další informace najdete v tématu [kvóty a omezení](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Vytvoření lokátorů streamování  

### <a name="not-encrypted"></a>Není šifrováno

Pokud chcete soubor streamovat v nejasném (nešifrovaném případě), nastavte předdefinované zásady pro zrušení streamování: na ' Predefined_ClearStreamingOnly ' (v .NET můžete použít výčet PredefinedStreamingPolicy. ClearStreamingOnly).

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

Pokud potřebujete šifrovat svůj obsah pomocí šifrování CENC, nastavte zásady na ' Predefined_MultiDrmCencStreaming '. Šifrování Widevine se použije na PŘERUŠOVANé streamy a PlayReady pro hladké. Klíč se doručí klientovi pro přehrávání na základě konfigurovaných licencí DRM.

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

Pokud chcete datový proud HLS šifrovat také pomocí CBCS (FairPlay), použijte Predefined_MultiDrmStreaming.

## <a name="associate-filters-with-streaming-locators"></a>Přidružit filtry ke Lokátorům streamování

Viz [filtry: přidružení ke lokátorům streamování](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filter, Order, entity lokátoru pro streamování stránek

Viz téma [filtrování, řazení, stránkování Media Services entit](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Vypsat Lokátory streamování podle názvu Assetu

Chcete-li získat Lokátory streamování na základě přidruženého názvu Assetu, použijte následující operace:

|Jazyk|Rozhraní API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|Rozhraní příkazového řádku|[AZ AMS Asset list-streaming-Lokátory](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="also-see"></a>Viz také

* [Aktiva](assets-concept.md)
* [Zásady streamování](streaming-policy-concept.md)
* [Zásady symetrických klíčů](content-key-policy-concept.md)

## <a name="next-steps"></a>Další kroky

[Kurz: nahrávání, kódování a streamování videí pomocí .NET](stream-files-tutorial-with-api.md)
