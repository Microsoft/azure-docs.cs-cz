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
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: a89d73b63680415aa8e516926b8e1d6c59ffbbad
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626021"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrování odpovědi, které jsou zahrnuty v odpovědi na vyhledávání  

Při dotazování na webu Bingu vrátí všechny nalezené hledání souvisejícího obsahu. Například pokud vyhledávacímu dotazu je "dinghies řízení +", odpověď může obsahovat následující odpovědi:

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

Chcete-li filtrovat odpovědi vrácené Bing, použijte níže uvedené parametry dotazu při volání rozhraní API.  

### <a name="responsefilter"></a>ResponseFilter

Typ odpovědi, které Bing zahrne do odpovědi (třeba obrázků, videí a zpráv) lze filtrovat pomocí [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) parametr dotazu, což je čárkami oddělený seznam odpovědi. Odpověď bude součástí odpovědi, pokud Bingu najde souvisejícího obsahu pro něj. 

Vyloučit konkrétní odpovědi z odpovědi, jako jsou obrázky, předřaďte `-` znak typ odpovědi. Příklad:

```
&responseFilter=-images,-videos
```

Následující znázorňuje způsob použití `responseFilter` k žádosti o obrázků, videí a zpráv dinghies řízení. Při kódování řetězce dotazu do %2 C změnit čárky.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Následující příklad ukazuje odpověď na předchozí dotaz. Protože Bingu se nepovedlo najít relevantní video a výsledky zpráv, neobsahuje odpovědi je.

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

I když Bingu nevrátil výsledky videa a novinky v předchozí odpovědi, neznamená, videa a novinky obsah neexistuje. Jednoduše znamená, že na stránce nezahrnuli je. Ale pokud jste [stránky](./paging-webpages.md) prostřednictvím více výsledků, následujících stránkách by pravděpodobně zahrnutí. Navíc pokud zavoláte [API pro vyhledávání videí](../bing-video-search/search-the-web.md) a [rozhraní API pro vyhledávání zpráv](../bing-news-search/search-the-web.md) koncové body přímo, odpověď by pravděpodobně obsahoval výsledky.

Můžete se nedoporučuje používat `responseFilter` k získání výsledků z jediného rozhraní API. Pokud chcete obsah z jediného rozhraní API Bingu, přímo volejte toto rozhraní API. Například pokud chcete přijímat pouze obrázky, odeslat požadavek na koncový bod rozhraní API pro vyhledávání obrázků, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` nebo jeden z nich [Imagí](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) koncových bodů. Volání jedno rozhraní API je důležitý nejenom kvůli výkonu, ale protože rozhraní API pro konkrétní obsah nabízet lepší výsledky. Například můžete použít filtry, které nejsou k dispozici pro webové rozhraní API hledání k filtrování výsledků.  

### <a name="site"></a>Lokality

Chcete-li získat výsledky hledání z konkrétní domény, patří `site:` parametr v řetězci dotazu dotazu.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> V závislosti na dotazu, pokud použijete `site:` – operátor dotazu, je pravděpodobné, že odpověď může obsahovat obsah pro dospělé bez ohledu na to [bezpečné hledání](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) nastavení. Operátor `site:` byste měli používat, pouze pokud znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé.

### <a name="freshness"></a>Stáří

Chcete-li omezit rozsah výsledků webových odpovědí na webové stránky, které Bing zjištěných v určitém období, nastavte [aktuálnosti](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) dotazování parametrem pro jednu z následujících hodnot velká a malá písmena:

* `Day` – Vrátí webové stránky, které Bing zjištěny za posledních 24 hodin
* `Week` – Vrátí webové stránky, které Bing zjištěny za posledních 7 dní
* `Month` – Vrátí webové stránky, které zjištěny za posledních 30 dní

Tento parametr je také může nastavit na vlastní rozsah ve formuláři, `YYYY-MM-DD..YYYY-MM-DD`. 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Omezit výsledky a jedním datumovým, nastavte parametr aktuálnosti na konkrétní datum:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Výsledky mohou být webové stránky, které spadají mimo zadanou dobu, pokud počet webové stránky, které Bing odpovídají vašim kritériím filtru menší než počet webových stránek, které jste požadovali (nebo výchozí číslo, které Bing vrátí).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Omezení počtu odpovědi v odpovědi

Bing může vrátit více typů odpovědí v odpovědi JSON. Například, když odešlete dotaz na *řízení + dinghies*, může vrátit Bingu `webpages`, `images`, `videos`, a `relatedSearches`.

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

Omezit počet odpovědí této Bing vrátí prvních dvou odpovědí (webové stránky a Image), sada [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) parametr 2 dotazu.

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

Pokud nahoře, seřazené odpovědi, které Bing vrátí pro dotaz webových stránek, obrázků, videí a relatedSearches, odpověď by obsahovat tyto odpovědi. Pokud nastavíte [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) do dvou (2), Bing vrátí nejvyšší dvě seřazený odpovědi: webové stránky a obrázky. Pokud chcete zahrnout obrázky a videa v odpovědi Bingu, zadejte [podporovat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) parametr dotazu a nastavte ho na obrázky a videa.

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
