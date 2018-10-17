---
title: 'Rychlý start: Rozhraní API Bingu pro vyhledávání entit'
description: Ukazuje, jak začít používat rozhraní API Bingu pro vyhledávání entit.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: ffc9ebb21c6646b1a39af4659053adf4157d204b
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48813953"
---
# <a name="quickstart-making-your-first-bing-entity-search-request"></a>Rychlý start: Vytvoření prvního požadavku na vyhledání entit v Bingu

Rozhraní API Bingu pro vyhledávání entit odešle vyhledávací dotaz do Bingu a načte výsledky, které zahrnují entity a místa. Mezi místa patří například restaurace, hotely nebo jiné místní firmy. U míst může dotaz obsahovat název místní firmy nebo může žádat o seznam (například restaurants near me). Mezi výsledky entit patří osoby, místa nebo věci. Místa jsou v tomto kontextu turistické atrakce, státy, země atd. 

## <a name="first-steps"></a>První kroky

Než budete moct provést první volání, je nutné nejprve získat klíč předplatného služeb Cognitive Services. Postup, jak získat klíč, naleznete v tématu [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api). (Pokud rozhraní API pro vyhledávání entit není v horní části vidět, klikněte na kartu **Hledat** a posouvejte dolů, dokud se nezobrazí.)

## <a name="the-endpoint"></a>Koncový bod

Pokud chcete získat výsledky hledání entit a míst, odešlete požadavek GET na následující koncový bod:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Požadavek musí používat protokol HTTPS.

Doporučujeme, aby byly všechny požadavky odeslány ze serveru. Distribuce klíče v rámci klientské aplikace nabízí více příležitostí pro přístup škodlivých třetích stran. Voláním ze serveru také zajistíte, že bude stačit upgradovat pouze jediný bod na budoucí verze rozhraní API.

## <a name="specifying-query-parameters-and-headers"></a>Zadání parametrů a hlaviček dotazu

Požadavek musí obsahovat parametr dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query), který obsahuje hledaný termín daného uživatele. Požadavek musí obsahovat také parametr dotazu [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt), který identifikuje trh, ze kterého chcete obdržet výsledky. Seznam volitelných parametrů dotazu najdete v tématu [Parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters). Všechny parametry dotazu se v adrese URL kódují.  
  
Požadavek musí obsahovat hlavičku [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey). Přestože jsou volitelné, doporučujeme, aby požadavek obsahoval i následující hlavičky:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

IP a hlavičky klienta jsou důležité pro vrácení obsahu závislého na umístění.  

Seznam všech hlaviček žádostí a odpovědí najdete v tématu [Hlavičky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>Požadavek

Následuje ukázka požadavku na entity, která obsahuje všechny navrhované parametry a hlavičky dotazu. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Pokud voláte některé z rozhraní API Bingu poprvé, nezahrnujte do volání hlavičku ID klienta. ID klienta zahrňte pouze v případě, že jste již dříve volali rozhraní API Bingu a Bing vrátil ID klienta pro příslušnou kombinaci uživatele a zařízení.

## <a name="the-response"></a>Odpověď

Následující příklad ukazuje odpověď na předchozí požadavek. Příklad také zobrazuje hlavičky odpovědi specifické pro Bing. Informace o objektu odpovědi najdete v části [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse).

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



## <a name="next-steps"></a>Další kroky

Vyzkoušejte si rozhraní API. Přejděte na téma [Testovací konzola rozhraní API pro vyhledávání entit](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/). 

Podrobnosti o využití objektů odpovědi najdete v tématu [Hledání entit a míst na webu](./search-the-web.md).

Nezapomeňte si přečíst [požadavky Bingu na zobrazení a použití](./use-display-requirements.md), abyste neporušili žádná pravidla používání výsledků hledání.
