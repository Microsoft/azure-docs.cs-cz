---
title: Rychlý start entity rozhraní API služby Search | Microsoft Docs
description: Ukazuje, jak začít používat rozhraní API služby Bing entity Search.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: B206A254-B7E9-49FF-AFD5-87B1E4D6D30B
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: 12031d2447920c7e2d6180f35cf4fb29aa1b6150
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342412"
---
# <a name="making-your-first-entities-request"></a>Provádění požadavků vaší první entity

Rozhraní API pro vyhledávání Entity odešle do služby Bing vyhledávací dotaz a získá výsledky, které zahrnují entity a místech. Místní výsledky obsahovat restaurace, hotelů nebo jiné místní podniky. Pro místech dotaz můžete zadat název místní firmy nebo můžete požádat seznam (například restaurace okolí). Entity výsledky obsahovat osob, míst nebo věcí. Místní v tomto kontextu je ubytovací atrakce stavy, zemích, atd. 

## <a name="first-steps"></a>První kroky

Předtím, než bude možné vytvářet první volání, které je potřeba získat předplatné klíč kognitivní služby. Získat klíč, najdete v části [zkuste kognitivní služby](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api). (Pokud rozhraní API pro vyhledávání entity není zobrazen v horní části, klepněte **vyhledávání** kartě a posuňte se dolů, dokud je neuvidíte.)

## <a name="the-endpoint"></a>Koncový bod

Pokud chcete získat entity a umístěte výsledky hledání, odeslat požadavek GET na následující koncový bod:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Žádost musí používat protokol HTTPS.

Doporučujeme vám, že všechny požadavky pocházejí ze serveru. Distribuce klíč v rámci klientské aplikace poskytuje další možnost pro třetí strana k přístupu. Navíc volání ze serveru poskytuje snadný upgrade pro budoucí verze rozhraní API.

## <a name="specifying-query-parameters-and-headers"></a>Určení parametrů dotazu a hlavičky

Musíte zadat požadavek [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) parametr dotazu, který obsahuje uživatele hledaný termín. Musíte zadat také požadavek [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt) parametr dotazu, který identifikuje na trhu, kam chcete výsledky pocházet z. Seznam parametrů dotazu volitelné, najdete v tématu [parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters). Adresa URL zakódovat všechny parametry dotazu.  
  
Musíte zadat požadavek [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey) záhlaví. I když je volitelné, vám doporučujeme, aby se také určit následující hlavičky:  
  
-   [Uživatelský Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge když](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [Umístění X vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

Hlavičky protokolu IP a umístění klienta jsou důležité pro vrácení obsahu vědět umístění.  

Seznam všech hlaviček žádostí a odpovědí najdete v tématu [hlavičky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>Požadavek

Následující obrázek znázorňuje požadavek entity, která zahrnuje všechny parametry navrhované dotazu a hlavičky. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Pokud je poprvé volání některé z rozhraní API služby Bing, neobsahují záhlaví ID klienta. Pokud jste dříve volat rozhraní API pro Bing a Bing vrátil ID klienta pro uživatele a zařízení kombinace pouze zahrnují ID klienta.

## <a name="the-response"></a>Odpověď

Následující obrázek znázorňuje odpověď na předchozí požadavek. Tento příklad také ukazuje hlavičky odpovědi specifické pro Bing. Informace o objektu odpovědi najdete v tématu [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse).

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```



## <a name="next-steps"></a>Další postup

Vyzkoušejte rozhraní API. Přejděte na [entity vyhledávání API testování konzoly](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/). 

Podrobnosti o spotřebě objektů odpovědi najdete v tématu [vyhledávání na webu pro entity a místech](./search-the-web.md).

Nezapomeňte si přečíst [požadavky zobrazení a použití Bing](./use-display-requirements.md) tak, že nemáte žádné z pravidel o používání výsledky hledání rozdělit.
