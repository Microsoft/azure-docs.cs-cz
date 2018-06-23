---
title: Webové rozhraní API služby Search úvodní | Microsoft Docs
description: Ukazuje, jak začít používat rozhraní API služby Bing webové Search.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 27B4B51A-D017-44C8-8E4E-9684DC553886
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 0b8c4678a518985a4be3ee426a85b0a85dd2365d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342486"
---
# <a name="your-first-bing-search-query"></a>Svůj první dotaz vyhledávání v Bingu

Předtím, než bude možné vytvářet první volání, které je potřeba získat předplatné klíč kognitivní služby. Získat klíč, najdete v části [zkuste kognitivní služby](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Výsledky hledání webové získáte by odeslat požadavek GET na následující koncový bod:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/search
```  

Žádost musí používat protokol HTTPS.

Doporučujeme vám, že všechny požadavky pocházejí ze serveru. Distribuce klíč v rámci klientské aplikace poskytuje další možnost pro škodlivý třetích stran k přístupu. Navíc volání ze serveru poskytuje snadný upgrade pro budoucí verze rozhraní API.  
  
Musíte zadat požadavek [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query) parametr dotazu, který obsahuje uživatele hledaný termín. Přestože je volitelné, žádost by také určit [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#mkt) parametr dotazu, který identifikuje na trhu, kam chcete výsledky pocházet z. Seznam volitelné dotaz parametry `responseFilter` a `textDecorations`, najdete v části [parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query-parameters). Všechny hodnoty parametru dotazu musí být kódovaná adresou URL.  
  
Musíte zadat požadavek [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#subscriptionkey) záhlaví. I když je volitelné, vám doporučujeme, aby se také určit následující hlavičky:  
  
-   [Uživatelský Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#clientid)  
-   [X-vyhledávání – když](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#clientip)  
-   [Umístění X vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#location)  

Hlavičky protokolu IP a umístění klienta jsou důležité pro vrácení obsahu vědět umístění. Například pokud se dotaz uživatele *řízení + lekce*, jsou pravděpodobně zajímat lekce umístěné nedaleko jejich umístění. Pokud chcete výsledky tak, aby obsahovala lekce, které jsou k dispozici blízkosti umístění uživatele, musíte zahrnout hlavička umístění a volitelně záhlaví IP klienta. Je méně důležité, pokud termín dotazu explicitně uvádí umístění (například řízení + lekce + florida + klíčů). 

Seznam všech hlaviček žádostí a odpovědí najdete v tématu [hlavičky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers).

## <a name="the-request"></a>Požadavek

Na obrázku je žádost o vyhledávání obsahující všechny parametry navrhované dotazu a hlavičky. Pokud je poprvé volání některé z rozhraní API služby Bing, neobsahují záhlaví ID klienta. Pokud jste dříve volat rozhraní API pro Bing a Bing vrátil ID klienta pro uživatele a zařízení kombinace pouze zahrnují ID klienta. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+lessons+seattle&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Následující obrázek znázorňuje odpověď na předchozí požadavek. Tento příklad také ukazuje hlavičky odpovědi specifické pro Bing.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Seattle Sailing Club | Lessons, Membership & More",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/seattlesailing.com",
            "snippet" : "Sailing is fun! Seattle Sailing Club makes it accessible and affordable...",
            "deepLinks" : [{
                "name" : "Learn to Sail",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD..."
            },
            {
                "name" : "Fleet",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38...",
                "snippet" : "The Seattle Sailing Club is nothing without our fleet of excellent..."
            },
            {
                "name" : "Sailing Lessons",
                "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38...",
                "snippet" : "Sailing Lessons. Taking the first step into the sailing community..."
            },
            {
                "name" : "Membership",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD3...",
                "snippet" : "Seattle sailboat rental for less than the cost of moorage!"
            },
            {
                "name" : "Hours – Directions",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874...",
                "snippet" : "Contact the Seattle Sailing Club by phone or email..."
            },
            {
                "name" : "Overnight Cruising",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD...",
                "snippet" : "Overnight Cruising. Setting sail for a multi-day adventure..."
            }],
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1",
            "name" : "Best Sailing lessons in Seattle, WA - yelp.com",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38AA...",
            "displayUrl" : "https:\/\/www.yelp.com\/search?find_desc=sailing+lessons&find_loc...",
            "snippet" : "Reviews on Sailing lessons in Seattle, WA - Seattle Sailing...",
            "dateLastCrawled" : "2017-04-01T10:49:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2",
            "name" : "Seattle Sailing with Windworks Sailing Club - Sailing ...",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD3...",
            "displayUrl" : "https:\/\/www.windworkssailing.com",
            "snippet" : "Seattle sailing club for sailing lessons, sailboat charters...",
            "dateLastCrawled" : "2017-04-09T03:33:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3",
            "name" : "Sailing Lessons in Downtown Seattle - SheSails Seattle",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38...",
            "displayUrl" : "shesailsseattle.com",
            "snippet" : "SheSails Seattle is sailing lessons and sailboat charters in a fun...",
            "dateLastCrawled" : "2017-04-05T07:15:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4",
            "name" : "Seattle Sailing Club - 19 Photos & 27 Reviews - Boating ...",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.yelp.com\/biz\/seattle-sailing-club-seattle",
            "snippet" : "27 reviews of Seattle Sailing Club \"Love the Seattle Sailing...",
            "dateLastCrawled" : "2017-04-08T14:40:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5",
            "name" : "Puget Sound Sailing - Seattle and Tacoma - PUGET SOUND ...",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDA...",
            "displayUrl" : "www.pugetsoundsailing.com",
            "snippet" : "Puget Sound Sailing Institute is an American Sailing Association...",
            "dateLastCrawled" : "2017-04-09T01:39:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.6",
            "name" : "Seattle Yacht Club - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "www.seattleyachtclub.org",
            "snippet" : "Seattle Yacht Club Awarded Joe Prosser Trophy for Excellence...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        }],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        },
        {
            "text" : "sailing schools seattle",
            "displayText" : "sailing schools seattle",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4..."
        },
        {
            "text" : "seattle boating lessons",
            "displayText" : "seattle boating lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4..."
        },
        {
            "text" : "adult sailing lessons seattle",
            "displayText" : "adult sailing lessons seattle",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED..."
        },
        {
            "text" : "seattle sailing club",
            "displayText" : "seattle sailing club",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED459..."
        },
        {
            "text" : "seattle sailing club lessons",
            "displayText" : "seattle sailing club lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289..."
        },
        {
            "text" : "sailing in seattle area",
            "displayText" : "sailing in seattle area",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED45948..."
        },
        {
            "text" : "seattle sailing lessons lake union",
            "displayText" : "seattle sailing lessons lake union",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED459..."
        }]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 6,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.6"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

## <a name="next-steps"></a>Další postup

Vyzkoušejte rozhraní API. Přejděte na [webové konzole testování vyhledávání rozhraní API](https://dev.cognitive.microsoft.com/docs/services/56b43eeccf5ff8098cef3807/operations/56b4447dcf5ff8098cef380d). 

Podrobnosti o spotřebě objektů odpovědi najdete v tématu [vyhledávání na webu](./search-the-web.md).