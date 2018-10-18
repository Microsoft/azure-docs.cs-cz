---
title: 'Rychlý start: Rozhraní API pro kontrolu pravopisu Bingu'
titlesuffix: Azure Cognitive Services
description: Ukazuje, jak začít používat rozhraní API Bingu pro kontrolu pravopisu.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 29ee7cb4ee648d20b425939553ba31cd9ac150f0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804187"
---
# <a name="quickstart-your-first-spell-check-request"></a>Rychlý start: První požadavek na kontrolu pravopisu

Před prvním voláním je potřeba získat klíč předplatného služeb Cognitive Services. Postup, jak získat klíč, naleznete v tématu [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api).

Pokud chcete zkontrolovat pravopisné a gramatické chyby v textovém řetězci, odešlete požadavek GET do následujícího koncového bodu:  
  
```
https://api.cognitive.microsoft.com/bing/v5.0/spellcheck
```

> [!NOTE]
> Koncový bod v7 Preview:
> 
> ```
> https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
> ```  
  
Požadavek musí používat protokol HTTPS.

Doporučujeme, aby byly všechny požadavky odeslány ze serveru. Distribuce klíče v rámci klientské aplikace nabízí více příležitostí pro přístup škodlivých třetích stran. Voláním ze serveru také zajistíte, že bude stačit upgradovat pouze jediný bod na budoucí verze rozhraní API.

Požadavek musí obsahovat parametr dotazu [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#text), který obsahuje textový řetězec ke kontrole. Požadavek by měl obsahovat taky parametr [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#mkt) dotazu, který identifikuje trh, ze kterého chcete obdržet výsledky, i když je tento parametr volitelný. Seznam volitelných parametrů dotazu, jako `mode`, naleznete v tématu [Parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#query-parameters). Všechny hodnoty parametru dotazu musí mít kódování URL.  
  
Požadavek musí obsahovat hlavičku [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#subscriptionkey). Přestože jsou volitelné, doporučujeme, aby požadavek obsahoval i následující hlavičky:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#location)  

Seznam všech hlaviček žádostí a odpovědí najdete v tématu [Hlavičky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#headers).

## <a name="the-request"></a>Požadavek

Následuje ukázka požadavku, který obsahuje všechny navrhované parametry a hlavičky dotazu. Pokud voláte některé z rozhraní API Bingu poprvé, nezahrnujte do volání hlavičku ID klienta. ID klienta zahrňte pouze v případě, že jste již dříve volali rozhraní API Bingu a Bing vrátil ID klienta pro příslušnou kombinaci uživatele a zařízení. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v5.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Požadavek v7 Preview:

> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Následující příklad ukazuje odpověď na předchozí požadavek. Příklad také zobrazuje hlavičky odpovědi specifické pro Bing.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  


## <a name="next-steps"></a>Další kroky

Vyzkoušejte si rozhraní API. Přejděte do [konzoly testování rozhraní API pro kontrolu pravopisu](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358). 

Podrobnosti o využití objektů odpovědi najdete v informacích o [textových řetězcích kontroly pravopisu](./proof-text.md).

