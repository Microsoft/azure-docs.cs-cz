---
title: Jak filtrovat výsledky hledání – rozhraní API pro vyhledávání na webu Bingu
titleSuffix: Azure Cognitive Services
description: Můžete filtrovat typy odpovědí, které Bing obsahuje v odpovědi (například obrázky, videa a zprávy) pomocí parametru dotazu responseFilter.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 6fa022f181e2061c6a7f3e08d1f2f501ddd9cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220267"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrování odpovědí, které odpověď na vyhledávání obsahuje  

Při dotazování na web vrátí Bing veškerý relevantní obsah, který vyhledá pro hledání. Pokud je například vyhledávací dotaz "sailing+dinghies", odpověď může obsahovat následující odpovědi:

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

## <a name="query-parameters"></a>Parametry dotazu

Chcete-li filtrovat odpovědi vrácené bingem, použijte při volání rozhraní API níže uvedené parametry dotazu.  

### <a name="responsefilter"></a>ResponseFilter

Můžete filtrovat typy odpovědí, které Bing obsahuje v odpovědi (například obrázky, videa a zprávy) pomocí parametru dotazu [responseFilter,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) který je čárkou oddělený seznam odpovědí. Odpověď bude zahrnuta v odpovědi, pokud Bing najde relevantní obsah pro něj. 

Chcete-li vyloučit konkrétní odpovědi z odpovědi, `-` jako jsou obrázky, předřávat znak typu odpovědi. Například:

```
&responseFilter=-images,-videos
```

Následující ukazuje, jak `responseFilter` používat k vyžádání obrázků, videí a novinek o plachtění člunů. Když zakódujete řetězec dotazu, čárky se změní na %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Následující příklad ukazuje odpověď na předchozí dotaz. Vzhledem k tomu, že Bing nenalezl relevantní výsledky videa a zpráv, odpověď je nezahrnuje.

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

Přestože Bing nevrátil video a novinky výsledky v předchozí odpovědi, neznamená to, že video a zpravodajský obsah neexistuje. Jednoduše to znamená, že je stránka neobsahovala. Pokud však [projdete dalšími](./paging-webpages.md) výsledky, budou je následující stránky pravděpodobně obsahovat. Také pokud zavoláte rozhraní [API pro vyhledávání videa](../bing-video-search/search-the-web.md) a news [search API](../bing-news-search/search-the-web.md) koncové body přímo, odpověď by pravděpodobně obsahovat výsledky.

Nedoporučujeme používat `responseFilter` k získání výsledků z jednoho rozhraní API. Pokud chcete obsah z jednoho rozhraní API Bingu, zavolejte toto rozhraní API přímo. Chcete-li například přijímat pouze obrázky, odešlete `https://api.cognitive.microsoft.com/bing/v7.0/images/search` požadavek do koncového bodu rozhraní API pro vyhledávání obrázků nebo do jednoho z dalších koncových bodů [image.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) Volání jednotného rozhraní API je důležité nejen z důvodů výkonu, ale protože rozhraní API specifická pro obsah nabízejí bohatší výsledky. K filtrování výsledků můžete například použít filtry, které nejsou k dispozici pro rozhraní API pro vyhledávání na webu.  

### <a name="site"></a>Web

Chcete-li získat výsledky hledání z `site:` určité domény, zahrňte parametr dotazu do řetězce dotazu.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> V závislosti na dotazu, `site:` pokud použijete operátor dotazu, je pravděpodobné, že odpověď může obsahovat obsah pouze pro dospělé bez ohledu na nastavení [bezpečného vyhledávání.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) Operátor `site:` byste měli používat, pouze pokud znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé.

### <a name="freshness"></a>Freshness

Chcete-li omezit výsledky webových odpovědí na webové stránky, které bing objevil během určitého období, nastavte parametr [dotazu na čerstvost](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) na jednu z následujících hodnot bez rozlišování velkých a malých písmen:

* `Day`— Vrácení webových stránek, které Bing objevil během posledních 24 hodin.
* `Week`— Vrácení webových stránek, které Bing objevil během posledních 7 dnů
* `Month`— Vrácení webových stránek, které byly objeveny během posledních 30 dnů.

Tento parametr můžete také nastavit na vlastní rozsah `YYYY-MM-DD..YYYY-MM-DD`dat ve formuláři . 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Chcete-li výsledky omezit na jedno datum, nastavte parametr čerstvosti na určité datum:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Výsledky mohou zahrnovat webové stránky, které nespadají do zadaného období, pokud je počet webových stránek, které bing odpovídá vašim kritériím filtru, menší než počet požadovaných webových stránek (nebo výchozí číslo, které bing vrací).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Omezení počtu odpovědí v odpovědi

Bing může vrátit více typů odpovědí v odpovědi JSON. Pokud se například dotazujete *na sailing+dinghies*, `webpages`může bing vrátit , `images`, `videos`a `relatedSearches`.

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

Chcete-li omezit počet odpovědí, které bing vrátí na horní dvě odpovědi (webové stránky a obrázky), nastavte parametr dotazu [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) na 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Odpověď zahrnuje `webPages` pouze `images`a .

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

Pokud přidáte `responseFilter` parametr dotazu do předchozího dotazu a nastavíte jej na webové stránky a zprávy, odpověď obsahuje pouze webové stránky, protože zprávy nejsou seřazeny.

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

## <a name="promoting-answers-that-are-not-ranked"></a>Propagace odpovědí, které nejsou hodnoceny

Pokud se nejlépe seřazené odpovědi, které Bing vrátí pro dotaz, jsou webové stránky, obrázky, videa a souvisejícíVyhledávání, odpověď by tyto odpovědi zahrnovala. Pokud nastavíte [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) na dva (2), Bing vrátí první dvě seřazené odpovědi: webové stránky a obrázky. Pokud chcete, aby Bing do odpovědi zahrnul obrázky a videa, zadejte parametr [propojovacího](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) dotazu a nastavte ho na obrázky a videa.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Následuje odpověď na výše uvedený požadavek. Bing vrátí dvě nejlepší odpovědi, webové stránky a obrázky a propaguje videa do odpovědi.

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

Pokud nastavíte `promote` na novinky, odpověď neobsahuje odpověď na zprávy,&mdash;protože se nejedná o hodnocenou odpověď, můžete propagovat pouze hodnocené odpovědi.

Odpovědi, které chcete propagovat, se `answerCount` do limitu nezapočítávají. Pokud jsou například seřazené odpovědi zprávy, `answerCount` obrázky `promote` a videa a nastaveno na 1 a na zprávy, odpověď obsahuje zprávy a obrázky. Nebo pokud jsou seřazené odpovědi videa, obrázky a zprávy, odpověď obsahuje videa a zprávy.

Můžete použít `promote` pouze v `answerCount` případě, že zadáte parametr dotazu.
