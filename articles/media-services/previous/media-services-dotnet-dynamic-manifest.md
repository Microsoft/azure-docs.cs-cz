---
title: Vytváření filtrů pomocí sady Azure Media Services .NET SDK
description: V tomto tématu se dozvíte, jak vytvořit filtry, aby je klient mohl používat ke streamování konkrétních oddílů datového proudu. Media Services .NET SDK vytvoří dynamické manifesty pro dosažení tohoto selektivního streamování.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 862385d1117e0a1b5d2220c06885e0fd0ecfa4d7
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531623"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Vytváření filtrů pomocí sady Media Services .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Počínaje verzí 2,17 Media Services umožňuje definovat filtry pro vaše prostředky. Tyto filtry jsou pravidla na straně serveru, která zákazníkům umožňují vybrat, co dělají: přehrávání pouze části videa (místo přehrávání celého videa) nebo určení pouze podmnožiny zvukových a video verzí, které může zařízení zákazníka zpracovat (místo všech verzí přidružených k assetu). Toto filtrování prostředků se dosahuje prostřednictvím **dynamických manifestů**, které jsou vytvořené na základě žádosti zákazníka o streamování videa na základě zadaných filtrů.

Podrobnější informace týkající se filtrů a dynamického manifestu naleznete v tématu [Přehled dynamických manifestů](media-services-dynamic-manifest-overview.md).

V tomto článku se dozvíte, jak pomocí Media Services .NET SDK vytvářet, aktualizovat a odstraňovat filtry. 

Poznámka: Pokud filtr aktualizujete, může trvat až dvě minuty, než koncový bod streamování pravidel aktualizuje pravidla. Pokud byl obsah obsluhován pomocí tohoto filtru (a ukládá do mezipaměti proxy a mezipaměti CDN), může aktualizace tohoto filtru způsobit selhání přehrávače. Po aktualizaci filtru vždy vymažte mezipaměť. Pokud tato možnost není možná, zvažte použití jiného filtru. 

## <a name="types-used-to-create-filters"></a>Typy použité k vytváření filtrů
Při vytváření filtrů se používají tyto typy: 

* **IStreamingFilter**.  Tento typ je založený na následujícím [filtru](/rest/api/media/operations/filter) REST API.
* **IStreamingAssetFilter**. Tento typ je založený na následujících REST API [AssetFilter](/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Tento typ je založený na následujících REST API [PresentationTimeRange](/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** a **IFilterTrackPropertyCondition**. Tyto typy jsou založené na následujících rozhraních REST API [FilterTrackSelect a FilterTrackPropertyCondition](/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Vytváření, aktualizace, čtení a odstraňování globálních filtrů
Následující kód ukazuje, jak použít .NET k vytváření, aktualizaci, čtení a odstraňování filtrů assetů.

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

## <a name="createupdatereaddelete-asset-filters"></a>Vytváření, aktualizace, čtení a odstraňování filtrů assetů
Následující kód ukazuje, jak použít .NET k vytváření, aktualizaci, čtení a odstraňování filtrů assetů.

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


## <a name="build-streaming-urls-that-use-filters"></a>Vytváření streamování adres URL, které používají filtry
Informace o tom, jak publikovat a doručovat vaše assety, najdete v tématu [doručování obsahu zákazníkům – přehled](media-services-deliver-content-overview.md).

Následující příklady ukazují, jak přidat filtry na adresy URL streamování.

**MPEG DASH** 

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v4**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) V3**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)`

**Technologie Smooth Streaming**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)`


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Přehled dynamických manifestů](media-services-dynamic-manifest-overview.md)
