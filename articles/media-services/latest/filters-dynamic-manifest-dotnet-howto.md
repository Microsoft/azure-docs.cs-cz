---
title: Vytváření filtrů pomocí Azure Media Services v3 .NET SDK
description: Toto téma popisuje, jak vytvořit filtry, aby je váš klient mohl použít k vysílání datového proudu určitých částí datového proudu. Media Services vytváří dynamické manifesty k dosažení tohoto selektivního streamování.
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
ms.openlocfilehash: ef04b1b7b5030189482e89e26e4565397cbdd7c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779242"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Vytváření filtrů pomocí sady Media Services .NET SDK

Při doručování obsahu zákazníkům (streamování živých událostí nebo videa na vyžádání) může klient potřebovat větší flexibilitu, než je popsáno ve výchozím souboru manifestu datového zdroje. Azure Media Services umožňuje definovat filtry účtů a filtry datových zdrojů pro váš obsah. 

Podrobný popis této funkce a scénářů, kde se používá, naleznete [v tématu Dynamické manifesty](filters-dynamic-manifest-overview.md) a [filtry](filters-concept.md).

Toto téma ukazuje, jak pomocí sady Media Services .NET SDK definovat filtr pro datový zdroj Video on Demand a vytvořit [filtry účtů](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) a [filtry datových zdrojů](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Ujistěte se, že [zkontrolujte prezentaciTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Požadavky 

- Zkontrolujte [filtry a dynamické manifesty](filters-dynamic-manifest-overview.md).
- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md). Nezapomeňte si zapamatovat název skupiny prostředků a název účtu Mediální služby. 
- Získání informací [potřebných](access-api-cli-how-to.md) pro přístup k apim
- Kontrola [nahrávání, kódování a streamování pomocí služby Azure Media Services,](stream-files-tutorial-with-api.md) abyste zjistili, jak [začít používat .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Definování filtru  

V rozhraní .NET nakonfigurujete výběry stop pomocí tříd [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) a [FilterTrackPropertyCondition.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) 

Následující kód definuje filtr, který obsahuje všechny zvukové stopy, které jsou EC-3 a všechny stopy videa, které mají přenosový tok v rozsahu 0-1000000.

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

## <a name="create-account-filters"></a>Vytvořit filtry účtů

Následující kód ukazuje, jak pomocí rozhraní .NET vytvořit filtr účtu, který obsahuje všechny [výše definované](#define-a-filter)výběry stop . 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Vytvořit filtry datových zdrojů

Následující kód ukazuje, jak použít rozhraní .NET k vytvoření filtru majetku, který obsahuje všechny [výše definované](#define-a-filter)výběry stop . 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Přidružení filtrů k lokátoru streamování

Můžete zadat seznam datových zdrojů nebo filtrů účtů, který by se vztahoval na lokátor streamování. [Dynamická packager (streaming endpoint)](dynamic-packaging-overview.md) použije tento seznam filtrů spolu s těmi, které váš klient určí v adrese URL. Tato kombinace generuje [dynamický manifest](filters-dynamic-manifest-overview.md), který je založen na filtrech v url + filtrech, které zadáte v lokátoru streamování. Doporučujeme použít tuto funkci, pokud chcete použít filtry, ale nechcete vystavit názvy filtrů v adrese URL.

Následující kód jazyka C# ukazuje, jak vytvořit `StreamingLocator.Filters`lokátor streamování a zadat . Toto je volitelná `IList<string>` vlastnost, která přebírá názvy filtrů.

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
      
## <a name="stream-using-filters"></a>Streamování pomocí filtrů

Jakmile definujete filtry, vaši klienti je mohou použít v adrese URL streamování. Filtry lze použít pro adaptivní protokoly datových proudů: Apple HTTP Live Streaming (HLS), MPEG-DASH a Smooth Streaming.

V následující tabulce jsou uvedeny některé příklady adres URL s filtry:

|Protocol (Protokol)|Příklad|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Další kroky

[Streamování videí](stream-files-tutorial-with-api.md) 


