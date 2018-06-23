---
title: Vyhledávání na webu trendů obrázků | Microsoft Docs
description: Ukazuje, jak používat rozhraní API služby Bing bitové kopie Search vyhledávání na webu pro trendů bitové kopie.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b12524cd4c1896501820209b3a45746b8f38b210
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342395"
---
# <a name="get-trending-images"></a>Získat trendů bitové kopie  

Získat dnešní trendů obrázky, odešlete následující požadavek GET:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Rozhraní API bitové kopie trendů aktuálně podporuje pouze následující trhy:  

- en US (angličtina, USA)  
- en-CA (angličtina, Kanada)  
- en-AU (angličtina, Austrálie)  
- zh-CN (čínština, Čína)

Obsahuje odpovědi [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#trendingimages) objekt, který uvádí Image podle kategorie. Použít kategorie `title` k seskupení obrázků v prostředí uživatele. Kategorie mohou změnit denně.  
  
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
  
Každou dlaždici obsahuje bitovou kopii a možnosti pro získání související bitové kopie. Pokud chcete získat související bitové kopie, můžete použít dotaz `text` k volání [rozhraní API pro vyhledávání bitové kopie](./search-the-web.md) a zobrazit související obrázky sami. Nebo můžete použít adresu URL v `webSearchUrl` provést uživateli na Bing bitové kopie stránky s výsledky hledání, který obsahuje související bitových kopií. 

Při volání rozhraní API pro vyhledávání Image získat související bitové kopie, nastavte [id](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#id) parametr ID v dotazu `id` pole. Zadání ID zajišťuje, že odpověď obsahuje bitovou kopii (je v odpovědi první obrázek) a jeho souvisejících bitové kopie. Také nastavena [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#q) parametr na text v dotazu `query` objektu `text` pole.

Následující příklad ukazuje, jak používat ID bitové kopie k získání související bitové kopie pan Smith v předchozí odpovědi trendů API bitové kopie.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
