---
title: Vytváření filtrů pomocí sady Azure Media Services .NET SDK
description: Toto téma popisuje, jak vytvářet filtry, takže klient může používat na určité části datový proud stream. Služba Media Services vytvoří dynamických manifestů k selektivní Streamovat.
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
ms.date: 02/09/2019
ms.author: juliako;cenkdin
ms.openlocfilehash: 2ee2e85188c4294060ef3effdc2d443f604aff61
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003325"
---
# <a name="creating-filters-with-media-services-net-sdk-legacy"></a>Vytváření filtrů pomocí sady Media Services .NET SDK (starší verze)
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Od verze 2.17, Media Services vám umožní definovat filtry pro vaše prostředky. Tyto filtry jsou pravidla na straně serveru, která umožňují zákazníkům zadáte věci, jako je: přehrávání pouze část videa (namísto přehrání celý video), nebo zadejte pouze podmnožinu audio a video interpretace, které dokáže zpracovat zařízení zákazníka (nikoli z všechny interpretace, které jsou spojeny s asset). Toto filtrování vaše prostředky se dosahuje prostřednictvím **dynamické Manifest**, které jsou vytvořeny na vyžádání vašich zákazníků, abyste mohli Streamovat video podle zadané filtry.

Podrobnější informace týkající se filtry a dynamický Manifest, najdete v článku [dynamických manifestů přehled](media-services-dynamic-manifest-overview.md).

Tento článek ukazuje, jak používat sadu Media Services .NET SDK vytvářet, aktualizovat a odstraňovat filtry. 

Mějte na paměti, že pokud aktualizujete filtr, může trvat až dvě minuty pro koncový bod aktualizovat pravidla streamování. Pokud se obsah vyřídila pomocí tohoto filtru (a uložit do mezipaměti v proxy servery a CDN mezipaměti), aktualizuje tento filtr může způsobit selhání přehrávače. Vždy vymažte mezipaměť po aktualizaci filtr. Pokud tato možnost není možné, zvažte použití jiného filtru. 

## <a name="types-used-to-create-filters"></a>Typy, které slouží k vytváření filtrů
Při vytváření filtrů se používají následující typy: 

* **IStreamingFilter**.  Tento typ je založen na následující rozhraní REST API [filtru](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Tento typ je založen na následující rozhraní REST API [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Tento typ je založen na následující rozhraní REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** a **IFilterTrackPropertyCondition**. Tyto typy jsou založeny na následující rozhraní REST API [FilterTrackSelect a FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Vytvoření, aktualizaci, čtení nebo odstranění globálních filtrů
Následující kód ukazuje, jak pomocí .NET k vytváření, aktualizaci, čtení a odstranění prostředku filtrů.

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

## <a name="createupdatereaddelete-asset-filters"></a>Vytvoření, aktualizaci, čtení nebo odstranění prostředku filtry
Následující kód ukazuje, jak pomocí .NET k vytváření, aktualizaci, čtení a odstranění prostředku filtrů.

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


## <a name="build-streaming-urls-that-use-filters"></a>Vytvoření adresy URL, které používají filtry pro streamování
Informace o tom, jak publikovat a doručovat vaše prostředky, najdete v části [doručování obsahu zákazníkům přehled](media-services-deliver-content-overview.md).

Následující příklady ukazují, jak přidat filtry vaší adresy URL pro streamování.

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Technologie Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Přehled dynamických manifestů](media-services-dynamic-manifest-overview.md)

