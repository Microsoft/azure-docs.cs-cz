---
title: Vytváření filtrů pomocí sady Azure Media Services .NET SDK
description: Toto téma popisuje, jak vytvářet filtry, takže klient může používat na určité části datový proud stream. Služba Media Services vytvoří dynamických manifestů k selektivní Streamovat.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 2bcb8762b94347f4409507fb89a18cb6c9d0dacd
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494294"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Vytváření filtrů pomocí sady Media Services .NET SDK

Při doručování obsahu zákazníkům (streamování živých událostí a videa na vyžádání) vašeho klienta může být nutné více flexibility než co je popsána v souboru manifestu výchozí asset. Azure Media Services umožňuje definovat účtu filtry a filtry asset pro obsah. 

Podrobný popis této funkce a scénáře, ve kterém se používá, najdete v části [dynamických manifestů](filters-dynamic-manifest-overview.md) a [filtry](filters-concept.md).

Toto téma ukazuje, jak pomocí Media Services .NET SDK můžete definovat filtr pro Video na vyžádání assetu a vytvořit [filtrů účtů](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) a [Asset filtry](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Přečtěte si [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Požadavky 

- Kontrola [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).
- [Vytvoření účtu Media Services](create-account-cli-how-to.md). Ujistěte se, že si pamatovat název skupiny prostředků a název účtu Media Services. 
- Získání informací potřebných k [přístup k rozhraním API](access-api-cli-how-to.md)
- Kontrola [nahrávání, kódování a streamování využívajícího službu Azure Media Services](stream-files-tutorial-with-api.md) zobrazíte jak [začít používat sadu .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Definujte filtr  

V rozhraní .NET, nakonfigurujte sledování výběrů [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) a [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) třídy. 

Následující kód definuje filtr, který zahrnuje všechny zvukové stopy, které jsou ES-3 a jakékoli video sledují, které mají s přenosovou rychlostí v 0 – 1 000 000 rozsahu.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Vytvoření účtu filtrů

Následující kód ukazuje, jak pomocí .NET vytvořit filtr účet, který zahrnuje všechny výběry sledování [výše](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Vytvoření assetu filtrů

Následující kód ukazuje, jak pomocí .NET vytvořit filtr asset, který zahrnuje všechny výběry sledování [výše](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Filtry přidružit Lokátor streamování

Můžete zadat seznam prostředků nebo účet filtrů, které pro vaše Lokátor streamování. [Dynamické Packager (koncový bod streamování)](dynamic-packaging-overview.md) platí tento seznam filtrů společně s ty klientem v adrese URL. Tato kombinace generuje [dynamické Manifest](filters-dynamic-manifest-overview.md), která je založena na filtry v adrese URL a filtry, které jste zadali na Lokátor streamování. Doporučujeme použít tuto funkci, pokud chcete použít filtry, ale nechcete, aby k vystavení filtr názvů v adrese URL.

Následující C# kód ukazuje, jak vytvořit lokátor streamování a zadejte `StreamingLocator.Filters`. Toto je volitelná vlastnost, která přebírá `IList<string>` filtru názvů.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Stream pomocí filtrů

Jakmile definujete filtry, klienty je použít v adrese URL streamování. Filtry můžete uplatnit adaptivní přenosové rychlosti streamování protokolů: Apple HTTP Live Streaming (HLS), MPEG-DASH a Smooth Streaming.

V následující tabulce jsou uvedeny příklady adresy URL s filtry:

|Protocol|Příklad:|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Další postup

[Stream videa](stream-files-tutorial-with-api.md) 


