---
title: Vytváření filtrů pomocí Azure Media Services V3 REST API
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780330"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Vytváření filtrů pomocí REST API služby Media Services

Při doručování obsahu zákazníkům (streamování živých událostí a videa na vyžádání) vašeho klienta může být nutné více flexibility než co je popsána v souboru manifestu výchozí asset. Azure Media Services umožňuje definovat účtu filtry a filtry asset pro obsah. 

Podrobný popis této funkce a scénářů, kde se používá, najdete v tématu [dynamické manifesty](filters-dynamic-manifest-overview.md) a [filtry](filters-concept.md).

Toto téma ukazuje, jak definovat filtr pro Video na vyžádání assetu a vytvořit pomocí rozhraní REST API [filtrů účtů](https://docs.microsoft.com/rest/api/media/accountfilters) a [Asset filtry](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Nezapomeňte zkontrolovat [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Požadavky 

K dokončení kroků popsaných v tomto tématu, budete muset:

- Kontrola [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).
- [Konfigurace nástroje Postman pro volání REST API služby Azure Media Services](media-rest-apis-with-postman.md).

    Nezapomeňte postupovat podle posledního kroku v tématu [získání tokenu Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definujte filtr  

Tady je **text žádosti** příklad, který definuje podmínky výběr sledování, které jsou přidány do manifestu. Tento filtr zahrnuje všechny zvukové stopy, které jsou EC-3, a všechny videosoubory, které mají přenosovou rychlost v rozsahu 0-1000000.

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

## <a name="create-account-filters"></a>Vytvoření účtu filtrů

Postman collection, který jste stáhli, vyberte **filtrů účtů**->**vytvořit nebo aktualizovat filtr účet**.

**UMÍSTIT** metoda požadavku HTTP je podobný:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Vyberte **tělo** kartu a vložte kód json [definovali dříve](#define-a-filter).

Vyberte **Poslat**. 

Filtr se vytvořil.

Další informace najdete v tématu [vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Vytvoření assetu filtrů  

V stažené kolekci "Media Services V3", kterou jste stáhli, vyberte **assets**->**vytvořit nebo aktualizovat filtr assetů**.

**UMÍSTIT** metoda požadavku HTTP je podobný:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Vyberte **tělo** kartu a vložte kód json [definovali dříve](#define-a-filter).

Vyberte **Poslat**. 

Filtr prostředků se vytvořila.

Podrobnosti o tom, jak vytvořit nebo aktualizovat prostředek filtry najdete v tématu [vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Přidružit filtry k lokátoru streamování

Můžete určit seznam filtrů Asset nebo Account, které se vztahují na Lokátor streamování. [Dynamický balíček (koncový bod streamování)](dynamic-packaging-overview.md) používá tento seznam filtrů společně s nastavením, které klient ZADÁ v adrese URL. Tato kombinace generuje [dynamický manifest](filters-dynamic-manifest-overview.md), který je založen na filtrech v URL + filtry, které zadáte na lokátoru streamování. Tuto funkci doporučujeme používat, pokud chcete použít filtry, ale nechcete vystavit názvy filtrů v adrese URL.

Pokud chcete vytvořit a přidružit filtry k lokátoru streamování pomocí REST, použijte [Lokátory streamování – vytvořit](https://docs.microsoft.com/rest/api/media/streaminglocators/create) rozhraní API a v [textu žádosti](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)zadejte `properties.filters`.
                                
## <a name="stream-using-filters"></a>Streamování pomocí filtrů

Po definování filtrů je můžou klienti používat v adrese URL streamování. Filtry mohou být aplikovány na protokoly streamování s adaptivní přenosovou rychlostí: Apple HTTP Live Streaming (HLS), MPEG-POMLČKa a Smooth Streaming.

V následující tabulce jsou uvedeny některé příklady adres URL s filtry:

|Protocol (Protokol)|Příklad:|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Další kroky

[Stream videa](stream-files-tutorial-with-rest.md) 
