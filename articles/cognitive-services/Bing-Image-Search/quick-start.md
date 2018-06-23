---
title: Bitové kopie rozhraní API služby Search úvodní | Microsoft Docs
description: Ukazuje, jak začít používat rozhraní API služby Bing bitové kopie Search.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BE2B2F8C-20B5-4E0B-AEC8-EAD505BA4C85
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 9e211cf5acd17ab80948d0b7161bdd2a9220c4a6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342444"
---
# <a name="your-first-images-search-query"></a>Vaše první Image vyhledávání dotazu

Předtím, než bude možné vytvářet první volání, které je potřeba získat předplatné klíč kognitivní služby Bing vyhledávání. Získat klíč, najdete v části [zkuste kognitivní služby](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api).

Pokud chcete získat výsledky hledání bitové kopie, by odeslat požadavek GET na následující koncový bod:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search
```
  
Žádost musí používat protokol HTTPS.

Doporučujeme vám, že všechny požadavky pocházejí ze serveru. Distribuce klíč v rámci klientské aplikace poskytuje další možnost pro škodlivý třetích stran k přístupu. Navíc volání ze serveru poskytuje snadný upgrade pro budoucí verze rozhraní API.

Musíte zadat požadavek [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) parametr dotazu, který obsahuje uživatele hledaný termín. Přestože je volitelné, žádost by také určit [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#mkt) parametr dotazu, který identifikuje na trhu, kam chcete výsledky pocházet z. Seznam volitelné dotaz parametry `freshness` a `size`, najdete v části [parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). Všechny hodnoty parametru dotazu musí být kódovaná adresou URL.  
  
Musíte zadat požadavek [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#subscriptionkey) záhlaví. I když je volitelné, vám doporučujeme, aby se také určit následující hlavičky:  
  
-   [Uživatelský Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientid)  
-   [X-vyhledávání – když](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientip)  
-   [Umístění X vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#location)  

Hlavičky protokolu IP a umístění klienta jsou důležité pro vrácení obsahu vědět umístění.  

Seznam všech hlaviček žádostí a odpovědí najdete v tématu [hlavičky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#headers).

## <a name="the-request"></a>Požadavek

Na obrázku je žádost o vyhledávání obsahující všechny parametry navrhované dotazu a hlavičky. Pokud je první čas volání některé z rozhraní API služby Bing, neobsahují záhlaví ID klienta. Pokud jste dříve volat rozhraní API pro Bing a Bing vrátil ID klienta pro uživatele a zařízení kombinace pouze zahrnují ID klienta. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Následující obrázek znázorňuje odpověď na předchozí požadavek.

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "totalEstimatedMatches" : 838,
    "value" : [
        {
            "name" : "File:Rich Passage Minto Sailing Dinghy.jpg - Wikipedia",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
            "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
            "datePublished" : "2011-10-29T11:26:00",
            "contentUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
            "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
            "contentSize" : "79239 B",
            "encodingFormat" : "jpeg",
            "hostPageDisplayUrl" : "en.wikipedia.org\/wiki\/File:Rich_Passage...",
            "width" : 526,
            "height" : 688,
            "thumbnail" : {
                "width" : 229,
                "height" : 300
            },
            "imageInsightsToken" : "ccid_GNarK7ma*mid_CCF85447ADA6...",
            "insightsSourcesSummary" : {
                "shoppingSourcesCount" : 0,
                "recipeSourcesCount" : 0
            },
            "imageId" : "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
            "accentColor" : "376094"
        },
        . . .
    ],
    "queryExpansions" : [
        {
            "text" : "Small Sailing Dinghies",
            "displayText" : "Small",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4...",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
            }
        },
        . . .
    ],
    "nextOffset" : 10,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Tender",
                    "displayText" : "Tender",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
                    }
                },
                . . .
            ]
        }
    ],
    "displayShoppingSourcesBadges" : false,
    "displayRecipeSourcesBadges" : true
}
```

## <a name="next-steps"></a>Další postup

Vyzkoušejte rozhraní API. Přejděte na [obrázek hledání API testovací konzolu](https://dev.cognitive.microsoft.com/docs/services/8336afba49a84475ba401758c0dbf749/operations/571fab09dbe2d933e891028f). 

Podrobnosti o spotřebě objektů odpovědi najdete v tématu [vyhledávání na webu](./search-the-web.md).

Podrobnosti o získání přehledy o image, jako jsou webové stránky, které obsahují image nebo osoby, které byly rozpoznány v bitové kopii, najdete v části [Image Insights](./image-insights.md).  
  
Podrobnosti o bitových kopiích, které jsou trendů na sociálních médiích najdete v tématu [trendů image](./trending-images.md).  
