---
title: Jak filtrovat výsledky hledání – rozhraní API webové vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak chcete filtrovat a zobrazit výsledky z rozhraní API webové vyhledávání Bingu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: scottwhi
ms.openlocfilehash: 945f89633060df7f57aa937be392149340acc21d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55855998"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrování odpovědi, které jsou zahrnuty v odpovědi na vyhledávání  

Při dotazování na webu Bingu vrátí veškerý obsah, který operátoru je relevantní pro hledání. Například pokud vyhledávacímu dotazu je "dinghies řízení +", odpověď může obsahovat následující odpovědi:

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

Pokud vás zajímají konkrétní typy obsahu, jako jsou obrázky, videa a novinky, mohou vyžádat pouze tyto odpovědi pomocí [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter) parametr dotazu. Pokud Bingu najde souvisejícího obsahu pro zadané odpovědi, Bing vrátí jej. Filtr odpovědí je čárkami oddělený seznam odpovědi. Následující znázorňuje způsob použití `responseFilter` k žádosti o obrázků, videí a zpráv dinghies řízení. Při kódování řetězce dotazu do %2 C změnit čárky.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Následující příklad ukazuje odpověď na předchozí dotaz. Jak je vidět Bingu nepovedlo se najít relevantní video a novinky výsledky, tak odpověď neobsahuje.

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

Pokud chcete vyloučit určité typy obsahu, jako jsou obrázky, z odpovědi, můžete je vyloučit s pomlčkou (minus) předpona k hodnotě responseFilter. Samostatné Vyloučené typy čárkou:

```
&responseFilter=-images,-videos
```

I když Bingu nevrátil výsledky videa a novinky v předchozí odpovědi, neznamená, videa a novinky obsah neexistuje. Jednoduše znamená, že na stránce nezahrnuli je. Ale pokud jste [stránky](./paging-webpages.md) prostřednictvím více výsledků, následujících stránkách by pravděpodobně zahrnutí. Navíc pokud zavoláte [API pro vyhledávání videí](../bing-video-search/search-the-web.md) a [rozhraní API pro vyhledávání zpráv](../bing-news-search/search-the-web.md) koncové body přímo, odpověď by pravděpodobně obsahoval výsledky.

Můžete se nedoporučuje používat `responseFilter` k získání výsledků z jediného rozhraní API. Pokud chcete obsah z jediného rozhraní API Bingu, přímo volejte toto rozhraní API. Například pokud chcete přijímat pouze obrázky, odeslat požadavek na koncový bod rozhraní API pro vyhledávání obrázků, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` nebo jeden z nich [Imagí](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#endpoints) koncových bodů. Volání jedno rozhraní API je důležitý nejenom kvůli výkonu, ale protože rozhraní API pro konkrétní obsah nabízet lepší výsledky. Například můžete použít filtry, které nejsou k dispozici pro webové rozhraní API hledání k filtrování výsledků.  

Chcete-li získat výsledky hledání z konkrétní domény, zahrnout `site:` – operátor dotazu v řetězci dotazu.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> V závislosti na dotazu, pokud použijete `site:` – operátor dotazu, je pravděpodobné, že odpověď může obsahovat obsah pro dospělé bez ohledu na to [bezpečné hledání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#safesearch) nastavení. Operátor `site:` byste měli používat, pouze pokud znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé.

## <a name="limiting-the-number-of-answers-in-the-response"></a>Omezení počtu odpovědi v odpovědi

Bing zahrnuje odpovědi v reakci na hodnocení. Například, když odešlete dotaz na *řízení + dinghies*, Bing vrátí `webpages`, `images`, `videos`, a `relatedSearches`.

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

Omezit počet odpovědí této Bing vrátí prvních dvou odpovědí (webové stránky a Image), sada [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) parametr 2 dotazu.

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

Pokud chcete přidat `responseFilter` parametr do předchozího dotazu a nastavte ho na webových stránkách a zpráv, odpověď obsahuje pouze webové stránky, protože není příspěvky seřazené dotazu.

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

## <a name="promoting-answers-that-are-not-ranked"></a>Zvyšuje se úroveň odpovědi, které nejsou seřazené.

Pokud nahoře, seřazené odpovědi, které Bing vrátí pro dotaz webových stránek, obrázků, videí a relatedSearches, odpověď by obsahovat tyto odpovědi. Pokud nastavíte [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) do dvou (2), Bing vrátí nejvyšší dvě seřazený odpovědi: webové stránky a obrázky. Pokud chcete zahrnout obrázky a videa v odpovědi Bingu, zadejte [podporovat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) parametr dotazu a nastavte ho na obrázky a videa.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Tady je odpověď na žádost výše. Bing vrátí nejvyšší dvě odpovědi, webové stránky a obrázků a podporuje videa do odpovědi.

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

Pokud nastavíte `promote` zpráv, neobsahuje odpovědi zpráv odpovědí, protože se nejedná o seřazený odpovědí&mdash;můžete zvýšit úroveň pouze seřazené odpovědi.

Odpovědi, které chcete podporovat nezapočítávají `answerCount` limit. Například, pokud jsou seřazený odpovědi, zprávy, obrázky a videa, a nastavíte `answerCount` na hodnotu 1 a `promote` o novinkách v odpovědi obsahuje zprávy a obrázky. Nebo, pokud seřazený odpovědi jsou videa, obrázky a novinky, odpověď obsahuje videí a zpráv.

Můžete zadat `promote` pouze v případě, že zadáte `answerCount` parametr dotazu.
