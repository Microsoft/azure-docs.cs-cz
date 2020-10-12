---
title: Postup filtrování výsledků hledání – rozhraní API Bingu pro vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Můžete filtrovat typy odpovědí, které Bing obsahuje v odpovědi (například obrázky, videa a novinky) pomocí parametru dotazu ' responseFilter '.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84696707"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrování odpovědí, které odpověď na hledání zahrnuje  

Při dotazování na web Bing vrátí veškerý relevantní obsah, který najde pro hledání. Například pokud je vyhledávací dotaz "" přidinghiesm + ", odpověď může obsahovat následující odpovědi:

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

## <a name="query-parameters"></a>Parametry dotazů

Pokud chcete filtrovat odpovědi vrácené bingem, použijte při volání rozhraní API níže uvedené parametry dotazu.  

### <a name="responsefilter"></a>ResponseFilter

Můžete filtrovat typy odpovědí, které Bing obsahuje v odpovědi (například obrázky, videa a novinky) pomocí parametru dotazu [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) , který je seznam odpovědí oddělených čárkami. Odpověď bude obsahovat odpověď v případě, že Bing najde relevantní obsah. 

Pokud chcete vyloučit konkrétní odpovědi z odpovědi, jako jsou obrázky, přiřaďte `-` k typu odpovědi znak. Například:

```
&responseFilter=-images,-videos
```

Následující příklad ukazuje, jak používat `responseFilter` k vyžádání obrázků, videí a zpráv o proplutí dinghies. Při kódování řetězce dotazu se čárky změní na% 2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Následující příklad ukazuje odpověď na předchozí dotaz. Vzhledem k tomu, že Bing nenalezl relevantní výsledky videa a zpráv, odpověď je nezahrne.

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

I když Bing nevrátil do předchozí odpovědi výsledky videa a zpráv, neznamená to, že obsah videa a zpráv neexistují. Prostě to znamená, že stránka je neobsahovala. Pokud však [stránku](./paging-webpages.md) provedete více výsledky, následné stránky je pravděpodobně budou zahrnovat. Také Pokud voláte rozhraní [vyhledávání videí API](../bing-video-search/search-the-web.md) a vyhledávání zpráv koncové body [rozhraní API](../bing-news-search/search-the-web.md) , odpověď pravděpodobně obsahuje výsledky.

Nedoporučujeme používat `responseFilter` k získání výsledků z jediného rozhraní API. Pokud chcete obsah z jednoho rozhraní API Bingu, zavolejte toto rozhraní API přímo. Například pro příjem pouze imagí, odeslání žádosti do koncového bodu rozhraní Vyhledávání obrázků API `https://api.cognitive.microsoft.com/bing/v7.0/images/search` nebo do některého z dalších koncových bodů [imagí](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) . Volání jediného rozhraní API je důležité nejen z důvodů výkonu, ale vzhledem k tomu, že rozhraní API pro konkrétní obsah nabízejí rozsáhlejší výsledky. Například můžete použít filtry, které nejsou k dispozici pro rozhraní Vyhledávání na webu API k filtrování výsledků.  

### <a name="site"></a>Web

Chcete-li získat výsledky hledání z konkrétní domény, zahrňte `site:` parametr dotazu do řetězce dotazu.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> V závislosti na dotazu, pokud použijete `site:` operátor dotazu, existuje možnost, že odpověď může obsahovat obsah pro dospělé bez ohledu na nastavení [bezpečné hledání](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) . Operátor `site:` byste měli používat, pouze pokud znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé.

### <a name="freshness"></a>Aktuálnost

Chcete-li omezit výsledky webové odpovědi na webové stránky, které Bing zjistil během konkrétního období, nastavte parametr dotazu [aktuálnosti](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) na jednu z následujících hodnot bez rozlišení velkých a malých písmen:

* `Day` – Vrátí webové stránky, které Bing zjistil během posledních 24 hodin.
* `Week` – Vrátí webové stránky, které Bing zjistil během posledních 7 dnů.
* `Month` – Vrátí webové stránky, které se zjistily během posledních 30 dnů.

Můžete také nastavit tento parametr na vlastní rozsah kalendářních dat ve formuláři `YYYY-MM-DD..YYYY-MM-DD` . 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Pokud chcete výsledky omezit na jedno datum, nastavte parametr aktuálnost na konkrétní datum:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Výsledky můžou zahrnovat webové stránky, které spadají mimo zadané období, pokud počet webových stránek, které Bing odpovídá kritériím filtru, je menší než počet požadovaných webových stránek (nebo výchozí číslo, které Bing vrátí).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Omezení počtu odpovědí v odpovědi

Bing může v odpovědi JSON vracet několik typů odpovědí. Například pokud se dotazuje na *dinghies +* aplikace, Bing může vracet `webpages` , `images` , `videos` a `relatedSearches` .

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

Pokud chcete omezit počet odpovědí, které Bing vrátí na horní dvě odpovědi (webové stránky a image), nastavte parametr dotazu [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) na hodnotu 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Odpověď obsahuje pouze `webPages` a `images` .

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

Pokud přidáte `responseFilter` parametr dotazu k předchozímu dotazu a nastavíte ho na webové stránky a novinky, odpověď obsahuje jenom webové stránky, protože novinky nejsou seřazené.

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

## <a name="promoting-answers-that-are-not-ranked"></a>Zvýšení úrovně odpovědí, které nejsou seřazené

Pokud se v horním pořadí odpovědí, které Bing vrátí pro dotaz, nacházejí webové stránky, obrázky, videa a relatedSearches, odpovědi by tyto odpovědi zahrnovaly. Pokud nastavíte [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) na dvě (2), Bing vrátí první dvě seřazené odpovědi: webové stránky a image. Pokud chcete, aby Bing zahrnoval obrázky a videa v odpovědi, zadejte parametr [povýšit](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) dotaz a nastavte ho na obrázky a videa.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Následuje odpověď na výše uvedený požadavek. Bing vrátí horní dvě odpovědi, webové stránky a obrázky a povýší videa na odpověď.

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

Pokud jste nastavili `promote` novinky, odpověď neobsahuje odpověď na zprávy, protože se nejedná o pokladnou odpověď, &mdash; můžete povýšit pouze seřazené odpovědi.

Odpovědi, které chcete zvýšit, se nepočítají na základě `answerCount` limitu. Například pokud jsou seřazené odpovědi zprávy, obrázky a videa a nastavíte hodnotu `answerCount` 1 a `promote` na zprávy, odpověď obsahuje zprávy a obrázky. Nebo, pokud jsou seřazené odpovědi videa, obrázky a novinky, odpověď obsahuje videa a novinky.

Můžete použít `promote` pouze v případě, že zadáte `answerCount` parametr dotazu.
