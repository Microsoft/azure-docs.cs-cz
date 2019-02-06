---
title: Získat populárních obrázků pomocí rozhraní API Bingu pro vyhledávání obrázků
titleSuffix: Azure Cognitive Services
description: Hledání dnešní populárních obrázků z webu pomocí rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 177a9cbf9235bb3d2deb0d5f8e4e31a0e1472b48
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756467"
---
# <a name="get-trending-images-from-the-web"></a>Získat populární imagí z webu

Chcete-li získat dnešní populárních obrázků, poslat následující požadavek GET:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Rozhraní API pro obrázky vzroste zájem o aktuálně podporuje pouze následujících zemích:  

- en US (angličtina, USA)  
- cs CA (angličtina, Kanada)  
- cs Austrálie (angličtina, Austrálie)  
- zh-CN (čínština, Čína)

Odpověď obsahuje [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#trendingimages) objekt, který obsahuje seznam imagí podle kategorie. Kategorie se používá `title` k seskupení imagí v činnost koncového uživatele. Kategorie může každý den vznikají.  

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

Každá dlaždice obsahuje bitovou kopii a možnosti pro získání související obrázky. Chcete-li získat související obrázky, můžete použít dotaz `text` volat [API pro vyhledávání obrázků](./search-the-web.md) a zobrazit související obrázky sami. Nebo můžete použít na adresu URL v `webSearchUrl` umožní uživateli Bingu imagí stránky s výsledky hledání, který obsahuje související obrázky.

Při volání rozhraní API pro vyhledávání obrázků získat související obrázky, nastavte [id](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#id) parametr ID v dotazu `id` pole. Určení ID zajistí, že odpověď obsahuje bitovou kopii (je to první obrázek v odpovědi) a související obrázky. Navíc nastavte [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) parametr na text v dotazu `query` objektu `text` pole.

Následující příklad ukazuje, jak získat související obrázky pan Smith v předchozí odpovědi vzroste zájem o rozhraní Image API pomocí ID bitové kopie.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
