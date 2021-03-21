---
title: Získání obrázků s využitím rozhraní API Bingu pro vyhledávání obrázků
titleSuffix: Azure Cognitive Services
description: Hledejte dnešní vývojové obrázky z webu pomocí rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: cf7d1baf895d44730eb913b658ee4c7fe7eb7b11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341610"
---
# <a name="get-trending-images-from-the-web"></a>Získat obrázky z vývoje z webu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Pokud chcete získat dnešní vývojové obrázky, pošlete následující požadavek GET:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Rozhraní API pro vývojové obrázky aktuálně podporuje jenom tyto trhy:  

- EN-US (angličtina, USA)  
- en-CA (angličtina, Kanada)  
- EN-AU (angličtina, Austrálie)  
- zh-CN (čínština, Čína)

Odpověď obsahuje objekt [TrendingImages](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) , který obsahuje obrázky podle kategorie. `title`K seskupení imagí v uživatelském prostředí použijte kategorii. Kategorie se můžou každý den měnit.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Každá dlaždice obsahuje obrázek a možnosti pro získání souvisejících imagí. Chcete-li získat související image, můžete použít dotaz `text` pro volání [rozhraní API vyhledávání obrázků](./overview.md) a zobrazení souvisejících imagí sami. Nebo můžete použít adresu URL v aplikaci `webSearchUrl` a převzít uživatele na stránku výsledků hledání imagí Bingu, která obsahuje související obrázky.

Pokud zavoláte rozhraní API Vyhledávání obrázků, abyste získali související image, nastavte parametr dotazu [ID](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) na ID v `id` poli. Zadání ID zajistí, že odpověď obsahuje obrázek (Jedná se o první obrázek v odpovědi) a jeho související obrázky. Také nastavte parametr dotazu [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) na text v `query` `text` poli objektu.

Následující příklad ukazuje, jak použít ID obrázku k získání souvisejících imagí Mr. Smith v předchozí reakci rozhraní API pro vývojové obrázky.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```