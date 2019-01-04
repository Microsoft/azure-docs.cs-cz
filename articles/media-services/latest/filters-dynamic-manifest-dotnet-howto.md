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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 31b49d882c1affbbef84bb6f4e8989d30fa320fa
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650960"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Vytváření filtrů pomocí sady Media Services .NET SDK

Při doručování obsahu zákazníkům (streamování živých událostí a videa na vyžádání) vašeho klienta může být nutné více flexibility než co je popsána v souboru manifestu výchozí asset. Azure Media Services umožňuje definovat účtu filtry a filtry asset pro obsah. Další informace najdete v tématu [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).

Toto téma ukazuje, jak pomocí Media Services .NET SDK můžete definovat filtr pro Video na vyžádání assetu a vytvořit [filtrů účtů](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) a [Asset filtry](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

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

## <a name="next-steps"></a>Další postup

[Stream videa](stream-files-tutorial-with-api.md) 


