---
title: Vytváření filtrů pomocí sady Azure Media Services .NET SDK
description: Toto téma popisuje, jak vytvářet filtry, takže vašeho klienta můžete použít datový proud určité části datového proudu. Služba Media Services vytvoří dynamické manifesty k dosažení této selektivní streamování.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako;cenkdin
ms.openlocfilehash: 04e6a1ac9b1fc94388580f03c6767da3da226c3a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="creating-filters-with-azure-media-services-net-sdk"></a>Vytváření filtrů pomocí sady Azure Media Services .NET SDK
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Od verze 2.17, Media Services umožňuje definovat filtry pro vaše prostředky. Tyto filtry jsou serverové pravidla, která umožňují vašim zákazníkům, kde můžete provádět například následující akce: přehrávání pouze část videa (namísto přehrávání celou video), nebo zadejte pouze podmnožinu interpretace audia a videa, které může zpracovat vašeho zákazníka zařízení (ne z všechny interpretace přidružených asset). Tento filtrování vaše prostředky je dosaženo pomocí **dynamické Manifest**ů, které jsou vytvořené na žádost zákazníka Streamovat videa podle zadané filtry.

Podrobné informace týkající se filtrů a dynamické Manifest, najdete v části [dynamické manifesty přehled](media-services-dynamic-manifest-overview.md).

Tento článek ukazuje, jak používat sadu Media Services .NET SDK vytvářet, aktualizovat a odstraňovat filtry. 

Poznámka: Pokud aktualizujete filtr, může trvat až dvě minuty pro koncový bod k aktualizaci pravidla streamování. Pokud obsah zpracování pomocí tohoto filtru (a uložené v mezipaměti v proxy servery a CDN mezipaměti), aktualizace tento filtr může způsobit selhání přehrávač. Po aktualizaci filtr vždy vymazání mezipaměti. Pokud tato možnost není možné, zvažte použití jiný filtr. 

## <a name="types-used-to-create-filters"></a>Typy používané pro vytvoření filtrů
Následující typy se používají při vytváření filtrů: 

* **IStreamingFilter**.  Tento typ je založena na následující volání rozhraní REST API [filtru](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Tento typ je založena na následující volání rozhraní REST API [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Tento typ je založena na následující volání rozhraní REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** a **IFilterTrackPropertyCondition**. Tyto typy jsou založené na následující rozhraní API REST [FilterTrackSelect a FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Vytvoření, aktualizace nebo pro čtení nebo odstranění globálních filtrů
Následující kód ukazuje, jak pomocí rozhraní .NET k vytváření, aktualizaci, přečtěte si a odstraňte asset filtry.

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

## <a name="createupdatereaddelete-asset-filters"></a>Vytvoření, aktualizace nebo pro čtení nebo odstranění asset filtry
Následující kód ukazuje, jak pomocí rozhraní .NET k vytváření, aktualizaci, přečtěte si a odstraňte asset filtry.

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
Informace o tom, jak publikovat a poskytovat vaše prostředky najdete v tématu [doručování obsahu zákazníkům přehled](media-services-deliver-content-overview.md).

Následující příklady ukazují, jak přidat filtry k adresám URL streamování.

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
[Přehled dynamické manifestů](media-services-dynamic-manifest-overview.md)

