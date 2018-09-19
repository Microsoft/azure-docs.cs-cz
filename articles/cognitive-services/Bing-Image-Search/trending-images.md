---
title: Hledat na webu pro populárních obrázků – rozhraní API pro vyhledávání obrázků Bingu
titleSuffix: Azure Cognitive Services
description: Další informace o použití rozhraní API pro vyhledávání obrázků Bingu pro vyhledávání na webu populárních obrázků.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 00d992da1d061ddbbee59e93898a92b6701f1ffb
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296307"
---
# <a name="get-trending-images-from-the-bing-image-search-api"></a>Získání populárních obrázků z rozhraní API Bingu pro vyhledávání obrázků

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

Při volání rozhraní API pro vyhledávání obrázků získat související obrázky, nastavte [id](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#id) parametr ID v dotazu `id` pole. Určení ID zajistí, že odpověď obsahuje bitovou kopii (je to první obrázek v odpovědi) a související obrázky. Navíc nastavte [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#q) parametr na text v dotazu `query` objektu `text` pole.

Následující příklad ukazuje, jak získat související obrázky pan Smith v předchozí odpovědi vzroste zájem o rozhraní Image API pomocí ID bitové kopie.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
