---
title: Rychlý start API kontrola pravopisu Bing | Microsoft Docs
description: Ukazuje, jak začít používat API kontrola pravopisu Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: AF8EB1F0-386D-4555-9354-735611435F04
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: article
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: cae8353e5be6e70eca90e5995b29b6774fc7d6a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342404"
---
# <a name="your-first-spell-check-request"></a>Kontrola pravopisu první žádosti o

Předtím, než bude možné vytvářet první volání, které je potřeba získat předplatné klíč kognitivní služby. Získat klíč, najdete v části [zkuste kognitivní služby](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api).

Zkontrolujte pravopis a gramatiku chyby textový řetězec, by odeslat požadavek GET na následující koncový bod:  
  
```
https://api.cognitive.microsoft.com/bing/v5.0/spellcheck
```

> [!NOTE]
> Koncový bod v7 Preview:
> 
> ```
> https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
> ```  
  
Žádost musí používat protokol HTTPS.

Doporučujeme vám, že všechny požadavky pocházejí ze serveru. Distribuce klíč v rámci klientské aplikace poskytuje další možnost pro škodlivý třetích stran k přístupu. Navíc volání ze serveru poskytuje snadný upgrade pro budoucí verze rozhraní API.

Musíte zadat požadavek [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#text) parametr dotazu, který obsahuje textový řetězec k ověření. I když je volitelné, žádost by také určit [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#mkt) parametr dotazu, který identifikuje na trhu, kam chcete výsledky pocházet z. Seznam volitelné dotaz parametry `mode`, najdete v části [parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#query-parameters). Všechny hodnoty parametru dotazu musí být kódovaná adresou URL.  
  
Musíte zadat požadavek [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#subscriptionkey) záhlaví. I když je volitelné, vám doporučujeme, aby se také určit následující hlavičky:  
  
-   [Uživatelský Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientid)  
-   [X-vyhledávání – když](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientip)  
-   [Umístění X vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#location)  

Seznam všech hlaviček žádostí a odpovědí najdete v tématu [hlavičky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#headers).

## <a name="the-request"></a>Požadavek

Na obrázku je požadavek, který zahrnuje všechny parametry navrhované dotazu a hlavičky. Pokud je poprvé volání některé z rozhraní API služby Bing, neobsahují záhlaví ID klienta. Pokud jste dříve volat rozhraní API pro Bing a Bing vrátil ID klienta pro uživatele a zařízení kombinace pouze zahrnují ID klienta. 
  
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
> Žádost o v7 Preview:

> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Následující obrázek znázorňuje odpověď na předchozí požadavek. Tento příklad také ukazuje hlavičky odpovědi specifické pro Bing.

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


## <a name="next-steps"></a>Další postup

Vyzkoušejte rozhraní API. Přejděte na [pravopisu kontrola rozhraní API testovací konzolu](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358). 

Podrobnosti o spotřebě objektů odpovědi najdete v tématu [textové řetězce kontrola pravopisu](./proof-text.md).

