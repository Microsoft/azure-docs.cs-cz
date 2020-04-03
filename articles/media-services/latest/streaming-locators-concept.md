---
title: Lokátory streamování ve službě Azure Media Services | Dokumenty společnosti Microsoft
description: Tento článek poskytuje vysvětlení, co jsou lokátory streamování a jak je používají Mediální služby Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 41b2d0ad1e072fb2bf5860ae80f8f25f886b37f7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582678"
---
# <a name="streaming-locators"></a>Lokátory streamování

Pokud chcete, aby videa ve výstupním prostředku byla k dispozici klientům pro přehrávání, musíte vytvořit [lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators) a adresy URL pro streamování. Chcete-li vytvořit adresu URL, musíte zřetězit název hostitele koncového bodu streamování a cestu lokátoru streamování. Ukázku v .NET najdete v tématu věnovaném [získání lokátoru streamování](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces vytváření **lokátoru streamování se** nazývá publikování. Ve výchozím nastavení je **lokátor streamování** platný ihned po volání rozhraní API a trvá, dokud není odstraněn, pokud nenakonfigurujete volitelný počáteční a koncový čas. 

Při vytváření **lokátoru datových proudů**je nutné zadat název **datového zdroje** a název **zásad streamování.** Další informace najdete v následujících tématech:

* [Prostředky](assets-concept.md)
* [Zásady streamování](streaming-policy-concept.md)
* [Zásady symetrických klíčů](content-key-policy-concept.md)

Můžete také zadat počáteční a koncový čas v lokátoru streamování, který umožní uživateli přehrávat obsah pouze mezi těmito časy (například mezi 5/1/2019 a 5/5/2019).  

## <a name="considerations"></a>Požadavky

* **Lokátory streamování** nelze aktualizovat. 
* Vlastnosti **lokátorů streamování,** které jsou typu Datetime, jsou vždy ve formátu UTC.
* Měli byste navrhnout omezenou sadu zásad pro váš účet Media Service a znovu je použít pro lokátory streamování, kdykoli jsou potřeba stejné možnosti. Další informace naleznete v [tématu Kvóty a omezení](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Vytvořit lokátory streamování  

### <a name="not-encrypted"></a>Není šifrováno

Pokud chcete streamovat soubor v jasném (nešifrovaném), nastavte předdefinovanou zásadu čistého streamování: na "Predefined_ClearStreamingOnly" (v rozhraní .NET můžete použít výčet PredefinedStreamingPolicy.ClearStreamingOnly).

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

Pokud potřebujete šifrovat obsah pomocí šifrování CENC, nastavte zásady na "Predefined_MultiDrmCencStreaming". Šifrování Widevine bude použito na datový proud DASH a playready k vyhlazení. Klíč bude doručen klientovi přehrávání na základě nakonfigurovaných licencí DRM.

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

Pokud chcete také šifrovat svůj HLS stream pomocí CBCS (FairPlay), použijte "Predefined_MultiDrmStreaming".

> [!NOTE]
> Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Přidružení filtrů k lokátorům streamování

Viz [Filtry: přidružení k vyhledávačům datových proudů](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrování, pořadí, stránky Streamování Lokátor entity

Viz [Filtrování, řazení, stránkování entit Mediálních služeb](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Seznam lokátorů streamování podle názvu datového zdroje

Chcete-li získat lokátory streamování na základě přidruženého názvu datového zdroje, použijte následující operace:

|Jazyk|rozhraní API|
|---|---|
|REST|[liststreaminglokátory](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|Rozhraní příkazového řádku|[az ams seznam datových proudů-lokátorů](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[SeznamStreamingLokátory](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Viz také

* [Prostředky](assets-concept.md)
* [Zásady streamování](streaming-policy-concept.md)
* [Zásady symetrických klíčů](content-key-policy-concept.md)
* [Kurz: Nahrávání, kódování a streamování videí pomocí rozhraní .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Další kroky

[Jak vytvořit vyhledávač datových proudů a vytvořit adresy URL](create-streaming-locator-build-url.md)
