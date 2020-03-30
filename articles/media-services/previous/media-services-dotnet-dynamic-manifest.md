---
title: Vytváření filtrů pomocí sady Azure Media Services .NET SDK
description: Toto téma popisuje, jak vytvořit filtry, aby je váš klient mohl použít k vysílání datového proudu určitých částí datového proudu. Media Services vytváří dynamické manifesty k dosažení tohoto selektivního streamování.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: cenkdin
ms.openlocfilehash: c60b223f91a151bf63cabc5e95816f2545022503
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016609"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Vytváření filtrů pomocí sady Media Services .NET SDK 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [Odpočinku](media-services-rest-dynamic-manifest.md)
> 
> 

Počínaje verzí 2.17 umožňuje služba Media Services definovat filtry pro vaše datové zdroje. Tyto filtry jsou pravidla na straně serveru, která zákazníkům umožňují zvolit si například: přehrávání pouze části videa (namísto přehrávání celého videa) nebo určení pouze podmnožinu zvukových a obrazových interpretací, které zařízení zákazníka zvládne (namísto všechny interpretace, které jsou přidruženy k prostředku). Tohoto filtrování vašich datových zdrojů je dosaženo prostřednictvím **dynamického manifestu,** které jsou vytvořeny na základě požadavku zákazníka na streamování videa na základě určených filtrů.

Podrobnější informace týkající se filtrů a dynamického manifestu naleznete [v tématu Přehled dynamických manifestů](media-services-dynamic-manifest-overview.md).

Tento článek ukazuje, jak pomocí sady Media Services .NET SDK vytvářet, aktualizovat a odstraňovat filtry. 

Poznámka: Pokud aktualizujete filtr, může trvat až dvě minuty, než koncový bod streamování aktualizuje pravidla. Pokud byl obsah obsluhován pomocí tohoto filtru (a ukládán do mezipaměti proxy a cdn), může aktualizace tohoto filtru způsobit selhání přehrávače. Po aktualizaci filtru vždy vymažte mezipaměť. Pokud tato možnost není možná, zvažte použití jiného filtru. 

## <a name="types-used-to-create-filters"></a>Typy používané k vytváření filtrů
Při vytváření filtrů se používají následující typy: 

* **IStreamingFilter**.  Tento typ je založen na následujícím filtru rozhraní REST [API.](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Tento typ je založen na následujícím filtru PROSTŘEDKŮ ROZHRANÍ REST [API AssetFilter.](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Tento typ je založen na následujících rozhraních REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** a **IFilterTrackPropertyCondition**. Tyto typy jsou založeny na následujících rest API [FilterTrackSelect a FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Vytvořit/aktualizovat/číst/odstranit globální filtry
Následující kód ukazuje, jak pomocí rozhraní .NET vytvářet, aktualizovat, číst a odstraňovat filtry datových zdrojů.

```csharp
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();
```

## <a name="createupdatereaddelete-asset-filters"></a>Vytvořit/aktualizovat/číst/odstranit filtry datových zdrojů
Následující kód ukazuje, jak pomocí rozhraní .NET vytvářet, aktualizovat, číst a odstraňovat filtry datových zdrojů.

```csharp
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();

```


## <a name="build-streaming-urls-that-use-filters"></a>Vytváření streamovaných adres URL s použitím filtrů
Informace o tom, jak publikovat a doručovat datové zdroje, najdete v [tématu Doručování obsahu zákazníkům Přehled](media-services-deliver-content-overview.md).

Následující příklady ukazují, jak přidat filtry do datových adres URL.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Živé vysílání (HLS) V4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Živé vysílání (HLS) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Technologie Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Přehled dynamických manifestů](media-services-dynamic-manifest-overview.md)

