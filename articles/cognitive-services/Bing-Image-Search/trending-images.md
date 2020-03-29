---
title: Získejte trendy obrázky pomocí rozhraní API pro vyhledávání obrázků Bingu
titleSuffix: Azure Cognitive Services
description: Vyhledejte dnešní populární obrázky z webu pomocí rozhraní API pro vyhledávání obrázků Bing.
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
ms.openlocfilehash: 2936b94d7ba791b1a4e5a9b95aca3ca3ecdb5904
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "66383435"
---
# <a name="get-trending-images-from-the-web"></a>Získání populárních obrázků z webu

Chcete-li získat dnešní trendy obrázky, pošlete následující požadavek GET:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Rozhraní API pro populární obrázky v současné době podporuje pouze následující trhy:  

- en-US (Angličtina, Spojené státy americké)  
- cs-CA (angličtina, Kanada)  
- en-AU (anglicky, Austrálie)  
- zh-CN (čínština, Čína)

Odpověď obsahuje [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) objekt, který obsahuje obrázky podle kategorie. Pomocí kategorie můžete `title` obrázky seskupit v uživatelském prostředí. Kategorie se mohou měnit každý den.  

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

Každá dlaždice obsahuje obrázek a možnosti pro získání souvisejících obrázků. Chcete-li získat související obrázky, `text` můžete pomocí dotazu volat [rozhraní API pro vyhledávání obrázků](./search-the-web.md) a zobrazit související obrázky sami. Nebo můžete použít adresu `webSearchUrl` URL v převést uživatele na stránku s výsledky hledání obrázků Bing, která obsahuje související obrázky.

Pokud zavoláte rozhraní API pro vyhledávání obrázků, abyste získali související obrázky, nastavte parametr dotazu [id](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) na ID v `id` poli. Zadání ID zajistí, že odpověď obsahuje obraz (jedná se o první obrázek v odpovědi) a související obrazy. Nastavte také parametr [dotazu q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) na `query` text `text` v poli objektu.

Následující příklad ukazuje, jak pomocí ID obrázku získat související obrázky pana Nováka v předchozí odpovědi rozhraní API trendy obrázky.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
