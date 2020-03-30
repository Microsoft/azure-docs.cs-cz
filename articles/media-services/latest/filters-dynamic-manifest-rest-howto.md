---
title: Vytváření filtrů pomocí rozhraní AZURE Media Services v3 REST API
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780330"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Vytváření filtrů pomocí rozhraní REST API mediálních služeb

Při doručování obsahu zákazníkům (streamování živých událostí nebo videa na vyžádání) může klient potřebovat větší flexibilitu, než je popsáno ve výchozím souboru manifestu datového zdroje. Azure Media Services umožňuje definovat filtry účtů a filtry datových zdrojů pro váš obsah. 

Podrobný popis této funkce a scénářů, kde se používá, naleznete [v tématu Dynamické manifesty](filters-dynamic-manifest-overview.md) a [filtry](filters-concept.md).

Toto téma ukazuje, jak definovat filtr pro datový zdroj Video on Demand a použít k vytvoření [filtrů účtů](https://docs.microsoft.com/rest/api/media/accountfilters) a [filtrů majetku](https://docs.microsoft.com/rest/api/media/assetfilters)použití api REST . 

> [!NOTE]
> Ujistěte se, že [zkontrolujte prezentaciTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Požadavky 

Chcete-li provést kroky popsané v tomto tématu, musíte:

- Zkontrolujte [filtry a dynamické manifesty](filters-dynamic-manifest-overview.md).
- [Konfigurace postman pro Azure Media Services REST API volání](media-rest-apis-with-postman.md).

    Ujistěte se, že postupujte podle posledního kroku v tématu [Získat Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definování filtru  

Následuje příklad **tělo požadavku,** který definuje podmínky výběru stopy, které jsou přidány do manifestu. Tento filtr obsahuje všechny zvukové stopy, které jsou EC-3 a všechny video stopy, které mají přenosový tok v rozsahu 0-1000000.

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

## <a name="create-account-filters"></a>Vytvořit filtry účtů

V kolekci Posla, kterou jste stáhli, vyberte **Filtry**->účtů**Vytvořit nebo aktualizovat filtr účtu**.

Metoda požadavku **PUT** HTTP je podobná:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Vyberte kartu **Tělo** a vložte kód json, který jste [definovali dříve](#define-a-filter).

Vyberte **Poslat**. 

Filtr byl vytvořen.

Další informace naleznete v [tématu Vytvoření nebo aktualizace](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Vytvořit filtry datových zdrojů  

V kolekci "Media Services v3" Postman, kterou jste stáhli, vyberte **položku Vytvořit**->**nebo aktualizovat filtr datových zdrojů**.

Metoda požadavku **PUT** HTTP je podobná:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Vyberte kartu **Tělo** a vložte kód json, který jste [definovali dříve](#define-a-filter).

Vyberte **Poslat**. 

Filtr majetku byl vytvořen.

Podrobnosti o vytváření nebo aktualizaci filtrů datových zdrojů naleznete v [tématu Vytvoření nebo aktualizace](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Přidružení filtrů k lokátoru streamování

Můžete zadat seznam datových zdrojů nebo filtrů účtů, který by se vztahoval na lokátor streamování. [Dynamická packager (streaming endpoint)](dynamic-packaging-overview.md) použije tento seznam filtrů spolu s těmi, které váš klient určí v adrese URL. Tato kombinace generuje [dynamický manifest](filters-dynamic-manifest-overview.md), který je založen na filtrech v url + filtrech, které zadáte v lokátoru streamování. Doporučujeme použít tuto funkci, pokud chcete použít filtry, ale nechcete vystavit názvy filtrů v adrese URL.

Chcete-li vytvořit a přidružit filtry k lokátoru streamování pomocí `properties.filters` funkce REST, použijte [lokátory streamování - vytvořte](https://docs.microsoft.com/rest/api/media/streaminglocators/create) rozhraní API a zadejte je v těle [požadavku](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Streamování pomocí filtrů

Jakmile definujete filtry, vaši klienti je mohou použít v adrese URL streamování. Filtry lze použít pro adaptivní protokoly datových proudů: Apple HTTP Live Streaming (HLS), MPEG-DASH a Smooth Streaming.

V následující tabulce jsou uvedeny některé příklady adres URL s filtry:

|Protocol (Protokol)|Příklad|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Další kroky

[Streamování videí](stream-files-tutorial-with-rest.md) 
