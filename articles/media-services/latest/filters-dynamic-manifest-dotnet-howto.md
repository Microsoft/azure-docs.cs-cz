---
title: Vytváření filtrů pomocí sady Azure Media Services V3 .NET SDK
description: V tomto tématu se dozvíte, jak vytvořit filtry, aby je klient mohl používat ke streamování konkrétních oddílů datového proudu. Media Services vytvoří dynamické manifesty pro dosažení tohoto selektivního streamování.
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
ms.openlocfilehash: 2226d7a769caf049af30bbca81ca3079d72970b4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023122"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Vytváření filtrů pomocí sady Media Services .NET SDK

Při doručování obsahu zákazníkům (streamování živých událostí nebo videa na vyžádání) může váš klient potřebovat větší flexibilitu než popis, který je popsaný v souboru manifestu výchozího prostředku. Azure Media Services vám umožní definovat filtry účtu a filtry assetů pro váš obsah. 

Podrobný popis této funkce a scénářů, kde se používá, najdete v tématu [dynamické manifesty](filters-dynamic-manifest-overview.md) a [filtry](filters-concept.md).

V tomto tématu se dozvíte, jak pomocí Media Services .NET SDK definovat filtr pro prostředek video na vyžádání a vytvořit [filtry účtu](/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) a [filtry assetů](/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Nezapomeňte zkontrolovat [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Předpoklady 

- Zkontrolujte [filtry a dynamické manifesty](filters-dynamic-manifest-overview.md).
- [Vytvořte účet Media Services](./create-account-howto.md). Nezapomeňte si pamatovat název skupiny prostředků a název účtu Media Services. 
- Získání informací potřebných pro [přístup k rozhraním API](./access-api-howto.md)
- Podívejte se na [nahrávání, kódování a streamování pomocí Azure Media Services,](stream-files-tutorial-with-api.md) abyste viděli, jak [začít používat sadu .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet) .

## <a name="define-a-filter"></a>Definovat filtr  

V rozhraní .NET nakonfigurujete výběry sledování pomocí tříd [FilterTrackSelection](/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) a [FilterTrackPropertyCondition](/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) . 

Následující kód definuje filtr, který obsahuje všechny zvukové stopy, které jsou ES-3, a všechny videosoubory, které mají přenosovou rychlost v rozsahu 0-1000000.

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

## <a name="create-account-filters"></a>Vytváření filtrů účtů

Následující kód ukazuje, jak použít .NET k vytvoření filtru účtu, který obsahuje všechny [výše uvedené](#define-a-filter)možnosti sledování. 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Vytváření filtrů assetů

Následující kód ukazuje, jak použít .NET k vytvoření filtru assetů, který obsahuje všechny [výše uvedené](#define-a-filter)možnosti sledování. 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Přidružit filtry k lokátoru streamování

Můžete určit seznam filtrů Asset nebo Account, které se vztahují na Lokátor streamování. [Dynamický balíček (koncový bod streamování)](dynamic-packaging-overview.md) používá tento seznam filtrů společně s nastavením, které klient ZADÁ v adrese URL. Tato kombinace generuje [dynamický manifest](filters-dynamic-manifest-overview.md), který je založen na filtrech v URL + filtry, které zadáte na lokátoru streamování. Tuto funkci doporučujeme používat, pokud chcete použít filtry, ale nechcete vystavit názvy filtrů v adrese URL.

Následující kód jazyka C# ukazuje, jak vytvořit Lokátor streamování a zadat `StreamingLocator.Filters` . Toto je volitelná vlastnost, která přebírá `IList<string>` názvy filtrů.

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

Po definování filtrů je můžou klienti používat v adrese URL streamování. Filtry mohou být aplikovány na protokoly streamování s adaptivní přenosovou rychlostí: Apple HTTP Live Streaming (HLS), MPEG-POMLČKa a Smooth Streaming.

V následující tabulce jsou uvedeny některé příklady adres URL s filtry:

|Protokol|Příklad|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Další kroky

[Streamování videí](stream-files-tutorial-with-api.md) 
