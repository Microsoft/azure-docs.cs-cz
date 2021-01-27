---
title: Lokátory streamování v Azure Media Services
description: Tento článek obsahuje vysvětlení toho, co jsou Lokátory streamování a jak se používají Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 9b711b9a6923a1923562a282dcaffe7cecb0c58b
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898134"
---
# <a name="streaming-locators"></a>Lokátory streamování

Pokud chcete, aby videa ve výstupním prostředku byla k dispozici klientům pro přehrávání, musíte vytvořit [lokátor streamování](/rest/api/media/streaminglocators) a adresy URL pro streamování. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele koncového bodu streamování a cestu k lokátoru streamování. Ukázku v .NET najdete v tématu věnovaném [získání lokátoru streamování](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces vytvoření **lokátoru streamování** se nazývá publikování. Ve výchozím nastavení je **Lokátor streamování** platný hned po volání rozhraní API a trvá až do odstranění, pokud nenastavíte volitelné počáteční a koncové časy. 

Při vytváření **lokátoru streamování** musíte zadat název **assetu** a název **zásady streamování** . Další informace najdete v následujících tématech:

* [Aktiva](assets-concept.md)
* [Zásady streamování](streaming-policy-concept.md)
* [Zásady pro klíč obsahu](content-key-policy-concept.md)

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

> [!NOTE]
> Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Přidružit filtry ke Lokátorům streamování

Viz [filtry: přidružení ke lokátorům streamování](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filter, Order, entity lokátoru pro streamování stránek

Viz téma [filtrování, řazení, stránkování Media Services entit](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Vypsat Lokátory streamování podle názvu Assetu

Chcete-li získat Lokátory streamování na základě přidruženého názvu Assetu, použijte následující operace:

|Jazyk|Rozhraní API|
|---|---|
|REST|[liststreaminglocators](/rest/api/media/assets/liststreaminglocators)|
|Rozhraní příkazového řádku|[AZ AMS Asset list-streaming-Lokátory](/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Viz také

* [Aktiva](assets-concept.md)
* [Zásady streamování](streaming-policy-concept.md)
* [Zásady pro klíč obsahu](content-key-policy-concept.md)
* [Kurz: nahrávání, kódování a streamování videí pomocí .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Další kroky

[Jak vytvořit Lokátor streamování a adresy URL sestavení](create-streaming-locator-build-url.md)
