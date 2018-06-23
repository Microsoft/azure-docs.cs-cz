---
title: Filtrování webové odpovědi, které vrací Bing | Microsoft Docs
description: Ukazuje, jak používat responseFilter vyfiltrujete odpovědi, které vrací rozhraní API služby Bing webové Search.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/12/2017
ms.author: scottwhi
ms.openlocfilehash: a5ee6241630ee24a05c2c4b932453bd7946a7508
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342870"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrování odpovědi, které jsou zahrnuty v odpovědi vyhledávání  

Při dotazu na webu, vrátí Bing veškerý obsah, který ho rootu předpokládá, že je relevantní pro hledání. Například pokud vyhledávací dotaz "řízení + dinghies", může obsahovat odpověď odpovědi na následující:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

Pokud vás zajímají konkrétní typy obsahu, jako je například obrázků, videí a zprávy, mohou vyžádat pouze tyto odpovědi pomocí [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter) parametr dotazu. Pokud Bing najde souvisejícího obsahu pro zadané odpovědi, vrátí Bing ji. Filtr odpovědi je čárkami oddělený seznam odpovědi. Následující ukazuje způsob použití `responseFilter` na žádost obrázků, videí a zprávy dinghies řízení. Při kódování řetězec dotazu, změňte čárky na %2 C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Následující obrázek znázorňuje odpověď na předchozí dotaz. Jak můžete vidět Bing nenašli relevantní výsledky video a zprávy, takže odpověď neobsahuje.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

I když Bing nevrátil výsledky video a zprávy v předchozí odpovědi, neznamená to, že obsah video a zprávy neexistuje. Jednoduše znamená, že stránce nezahrnuli je. Ale pokud jste [stránky](./paging-webpages.md) prostřednictvím více výsledků, následných stránkách by pravděpodobně nezahrnete. Navíc při volání [rozhraní API pro vyhledávání Video](../bing-video-search/search-the-web.md) a [rozhraní API pro vyhledávání zprávy](../bing-news-search/search-the-web.md) koncové body přímo, odpověď by pravděpodobně obsahoval výsledky. 

Můžete se nedoporučuje používat `responseFilter` se získat výsledky z jediného rozhraní API. Pokud chcete obsah z jediného rozhraní API služby Bing, volejte přímo toto rozhraní API. Například pokud chcete přijímat pouze obrázky, odeslat požadavek na koncový bod rozhraní API pro vyhledávání bitové kopie, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` nebo jeden z dalších [bitové kopie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#endpoints) koncové body. Volání metody jediného rozhraní API je důležité nejen z důvodů výkonu, ale protože rozhraní API specifické pro obsah nabízejí širší výsledky. Můžete například použít filtry, které nejsou k dispozici pro vyhledávání webového rozhraní API pro filtrování výsledků.  
  
Chcete-li získat výsledky vyhledávání z konkrétní domény, zahrňte `site:` – operátor dotazu v řetězci dotazu.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE] 
> V závislosti na dotazu, pokud použijete `site:` – operátor dotazu, je pravděpodobné, že odpověď může obsahovat obsah pro dospělé bez ohledu na to [bezpečné hledání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#safesearch) nastavení. Měli byste použít `site:` pouze v případě, že jste si vědomi obsahu v lokalitě a váš scénář podporuje možnost obsah pro dospělé. 
  
## <a name="limiting-the-number-of-answers-in-the-response"></a>Omezení počtu odpovědi v odpovědi

Bing zahrnuje odpovědi v reakci na hodnocení. Například, pokud je dotaz *řízení + dinghies*, vrátí Bing `webpages`, `images`, `videos`, a `relatedSearches`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Omezit počet odpovědi této Bing vrátí do odpovědi horních dvou (webové stránky a bitové kopie), sadu [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) parametr 2 dotazu. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Odpověď obsahuje pouze `webPages` a `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Pokud přidáte `responseFilter` parametr do předchozího dotazu a nastavte ho na webových stránkách a zprávy, odpovědi obsahuje pouze webové stránky, protože není seřazeny zprávy dotazu.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Povýšení odpovědi, které nejsou seřazeny

Pokud jsou seřazeny odpovědi, které vrací Bing dotazu horní webové stránky, obrázků, videí a relatedSearches, odpovědi bude zahrnovat tyto odpovědi. Pokud nastavíte [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) na dva (2), Bing vrátí nejvyšší dvě seřazený odpovědi: webové stránky a obrázků. Pokud chcete, Bing, které chcete zahrnout do odpovědi bitové kopie a videa, zadejte [povýšit](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) parametr dotazu a nastavte ji na obrázky a videa. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Toto je odpověď na žádost výše. Bing vrátí nejvyšší dvě odpovědi, webové stránky a bitové kopie a podporuje videa do odpovědi.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Pokud nastavíte `promote` pro zprávy, neobsahuje odpovědi odpovědí zprávy, protože není seřazený odpovědí&mdash;můžete zvýšit úroveň jen seřazeny odpovědi.

Odpovědi, které chcete zvýšit úroveň do počtu `answerCount` limit. Například, pokud seřazený odpovědi jsou novinky, Image a videa, a nastavíte `answerCount` na 1 a `promote` na zprávy, odpovědi obsahuje příspěvky a bitové kopie. Nebo pokud seřazený odpovědi jsou videa, Image a zprávy, odpovědi obsahuje videa a zprávy.

Můžete použít `promote` pouze v případě, že zadáte `answerCount` parametr dotazu.
