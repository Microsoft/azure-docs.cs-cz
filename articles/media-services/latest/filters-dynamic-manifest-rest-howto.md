---
title: Vytváření filtrů pomocí Azure Media Services – rozhraní REST API | Dokumentace Microsoftu
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
ms.openlocfilehash: 76e6e1595cb8bf49dbbc82c3cae5de80ea718aeb
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786449"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Vytváření filtrů pomocí REST API služby Media Services

Při doručování obsahu zákazníkům (streamování živých událostí a videa na vyžádání) vašeho klienta může být nutné více flexibility než co je popsána v souboru manifestu výchozí asset. Azure Media Services umožňuje definovat účtu filtry a filtry asset pro obsah. 

Podrobný popis této funkce a scénáře, ve kterém se používá, najdete v části [dynamických manifestů](filters-dynamic-manifest-overview.md) a [filtry](filters-concept.md).

Toto téma ukazuje, jak definovat filtr pro Video na vyžádání assetu a vytvořit pomocí rozhraní REST API [filtrů účtů](https://docs.microsoft.com/rest/api/media/accountfilters) a [Asset filtry](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Přečtěte si [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Požadavky 

K dokončení kroků popsaných v tomto tématu, budete muset:

- Kontrola [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).
- [Konfigurace nástroje Postman pro volání REST API služby Azure Media Services](media-rest-apis-with-postman.md).

    Ujistěte se, že chcete postupovat podle poslední krok v tomto tématu [získat Azure AD na Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definujte filtr  

Tady je **text žádosti** příklad, který definuje podmínky výběr sledování, které jsou přidány do manifestu. Tento filtr obsahuje všechny zvukové stopy, které jsou ES-3 a jakékoli video sledují, které mají s přenosovou rychlostí v 0 – 1 000 000 rozsahu.

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

V kolekci Postman "Služby Media Services v3", který jste stáhli, vyberte **prostředky**->**vytvořit nebo aktualizovat filtr Asset**.

**UMÍSTIT** metoda požadavku HTTP je podobný:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Vyberte **tělo** kartu a vložte kód json [definovali dříve](#define-a-filter).

Vyberte **Poslat**. 

Filtr prostředků se vytvořila.

Podrobnosti o tom, jak vytvořit nebo aktualizovat prostředek filtry najdete v tématu [vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Filtry přidružit Lokátor streamování

Můžete zadat seznam prostředků nebo účet filtrů, které pro vaše Lokátor streamování. [Dynamické Packager (koncový bod streamování)](dynamic-packaging-overview.md) platí tento seznam filtrů společně s ty klientem v adrese URL. Tato kombinace generuje [dynamické Manifest](filters-dynamic-manifest-overview.md), která je založena na filtry v adrese URL a filtry, které jste zadali na Lokátor streamování. Doporučujeme použít tuto funkci, pokud chcete použít filtry, ale nechcete, aby k vystavení filtr názvů v adrese URL.

Chcete-li vytvořit a přidružit filtry Lokátor streamování pomocí rozhraní REST, použijte [lokátory streamování – vytvořit](https://docs.microsoft.com/rest/api/media/streaminglocators/create) rozhraní API a zadejte `properties.filters` v [tělo požadavku](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Stream pomocí filtrů

Jakmile definujete filtry, klienty je použít v adrese URL streamování. Filtry můžete uplatnit adaptivní přenosové rychlosti streamování protokolů: Apple HTTP Live Streaming (HLS), MPEG-DASH a Smooth Streaming.

V následující tabulce jsou uvedeny příklady adresy URL s filtry:

|Protocol|Příklad|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Další kroky

[Stream videa](stream-files-tutorial-with-rest.md) 
