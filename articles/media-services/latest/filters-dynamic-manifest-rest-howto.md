---
title: Vytváření filtrů pomocí Azure Media Services V3 REST API
description: V tomto tématu se dozvíte, jak vytvořit filtry, aby je klient mohl používat ke streamování konkrétních oddílů datového proudu. Media Services V3 REST API vytvoří dynamické manifesty pro dosažení tohoto selektivního streamování.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c2d081ded07b1d32ee7525855c1756e13dfd57aa
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277500"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Vytváření filtrů pomocí Media Services REST API

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Při doručování obsahu zákazníkům (streamování živých událostí nebo videa na vyžádání) může váš klient potřebovat větší flexibilitu než popis, který je popsaný v souboru manifestu výchozího prostředku. Azure Media Services vám umožní definovat filtry účtu a filtry assetů pro váš obsah. 

Podrobný popis této funkce a scénářů, kde se používá, najdete v tématu [dynamické manifesty](filters-dynamic-manifest-concept.md) a [filtry](filters-concept.md).

V tomto tématu se dozvíte, jak definovat filtr pro prostředek video na vyžádání a pomocí rozhraní REST API vytvářet [filtry účtů](/rest/api/media/accountfilters) a [filtry assetů](/rest/api/media/assetfilters). 

> [!NOTE]
> Nezapomeňte zkontrolovat [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Požadavky 

K dokončení kroků popsaných v tomto tématu je třeba provést následující kroky:

- Zkontrolujte [filtry a dynamické manifesty](filters-dynamic-manifest-concept.md).
- [Nakonfigurujte metodu post pro Azure Media Services REST API volání](setup-postman-rest-how-to.md).

    Nezapomeňte postupovat podle posledního kroku v tématu [získání tokenu Azure AD](setup-postman-rest-how-to.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definovat filtr  

Následuje příklad **těla žádosti** , který definuje podmínky pro výběr sledování, které jsou přidány do manifestu. Tento filtr zahrnuje všechny zvukové stopy, které jsou EC-3, a všechny videosoubory, které mají přenosovou rychlost v rozsahu 0-1000000.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Vytváření filtrů účtů

V stažené kolekci po výběru vyberte **filtry účtu** -> **vytvořit nebo aktualizovat filtr účtu**.

Metoda **Put** http je podobná:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Vyberte kartu **tělo** a vložte kód JSON, který jste [definovali dříve](#define-a-filter).

Vyberte **Odeslat**. 

Filtr byl vytvořen.

Další informace najdete v tématu věnovaném [Vytvoření nebo aktualizaci](/rest/api/media/accountfilters/createorupdate). Podívejte se také na [Příklady JSON pro filtry](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Vytváření filtrů assetů  

V stažených kolekcích "Media Services V3", které jste stáhli, vyberte **assety** -> **vytvořit nebo aktualizovat filtr assetů**.

Metoda **Put** http je podobná:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Vyberte kartu **tělo** a vložte kód JSON, který jste [definovali dříve](#define-a-filter).

Vyberte **Odeslat**. 

Filtr assetu byl vytvořen.

Podrobnosti o tom, jak vytvořit nebo aktualizovat filtry assetu, najdete v tématu věnovaném [Vytvoření nebo aktualizaci](/rest/api/media/assetfilters/createorupdate). Podívejte se také na [Příklady JSON pro filtry](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Přidružit filtry k lokátoru streamování

Můžete určit seznam filtrů Asset nebo Account, které se vztahují na Lokátor streamování. [Dynamický balíček (koncový bod streamování)](encode-dynamic-packaging-concept.md) používá tento seznam filtrů společně s nastavením, které klient ZADÁ v adrese URL. Tato kombinace generuje [dynamický manifest](filters-dynamic-manifest-concept.md), který je založen na filtrech v URL + filtry, které zadáte na lokátoru streamování. Tuto funkci doporučujeme používat, pokud chcete použít filtry, ale nechcete vystavit názvy filtrů v adrese URL.

Pokud chcete vytvořit a přidružit filtry k lokátoru streamování pomocí REST, použijte [Lokátory streamování – vytvořit](/rest/api/media/streaminglocators/create) rozhraní API a `properties.filters` v [textu žádosti](/rest/api/media/streaminglocators/create#request-body)zadejte.
                                
## <a name="stream-using-filters"></a>Streamování pomocí filtrů

Po definování filtrů je můžou klienti používat v adrese URL streamování. Filtry mohou být aplikovány na protokoly streamování s adaptivní přenosovou rychlostí: Apple HTTP Live Streaming (HLS), MPEG-POMLČKa a Smooth Streaming.

V následující tabulce jsou uvedeny některé příklady adres URL s filtry:

|Protokol|Příklad|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Další kroky

[Streamování videí](stream-files-tutorial-with-rest.md) 
