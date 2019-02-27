---
title: Odesílání požadavků na rozhraní API Bingu pro kontrolu pravopisu zkontrolujte
titlesuffix: Azure Cognitive Services
description: Další informace o režimů pro kontrolu pravopisu Bingu, nastavení a další informace týkající se rozhraní API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 64025165a5a88370a02ba3b4554b1e12d36c8810
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889948"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Odesílání požadavků na rozhraní API Bingu pro kontrolu pravopisu zkontrolujte

Pokud chcete zkontrolovat pravopisné a gramatické chyby v textovém řetězci, odešlete požadavek GET do následujícího koncového bodu:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
Požadavek musí používat protokol HTTPS.

Doporučujeme, aby všechny požadavky pocházely ze serveru. Distribuce klíče v rámci klientské aplikace poskytuje škodlivým třetím stranám víc příležitostí získat k ní přístup. Server také poskytuje jediný bod upgradu pro budoucí verze rozhraní API.

Požadavek musí obsahovat parametr dotazu [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#text), který obsahuje textový řetězec ke kontrole. Požadavek by měl obsahovat taky parametr [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#mkt) dotazu, který identifikuje trh, ze kterého chcete obdržet výsledky, i když je tento parametr volitelný. Seznam volitelných parametrů dotazu, jako `mode`, naleznete v tématu [Parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#query-parameters). Všechny hodnoty parametru dotazu musí mít kódování URL.  
  
Požadavek musí obsahovat hlavičku [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#subscriptionkey). I když je volitelné, jsou ukončena. doporučujeme zadat také následující záhlaví. Tyto hlavičky pomoct vráceny přesnější výsledky API kontrola pravopisu Bingu:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#location)  

Seznam všech hlaviček žádostí a odpovědí najdete v části s [hlavičkami](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#headers).

Při volání rozhraní API Bingu pro kontrolu pravopisu zkontrolovat pomocí jazyka JavaScript, může přístup k hodnoty z těchto záhlaví zabránit v prohlížeči integrované bezpečnostní funkce.

Chcete-li vyřešit tento problém, můžete provést požadavek na API kontrola pravopisu Bingu prostřednictvím proxy serveru CORS. Odpověď od takový proxy server má `Access-Control-Expose-Headers` záhlaví této hlavičky odpovědi seznamů povolených a zpřístupňuje je pro jazyk JavaScript.

Je snadné k instalaci proxy CORS a povolit [ukázková aplikace](../tutorials/spellcheck.md) záhlaví volitelný klientský přístup. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Potom zadejte následující příkaz z příkazového řádku.

    npm install -g cors-proxy-server

V dalším kroku změňte API kontrola pravopisu Bingu koncového bodu v souboru HTML:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

    cors-proxy-server

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozšíření hlavičky protokolu HTTP níže v části výsledků hledání, nyní je vidět `X-MSEdge-ClientID` záhlaví (mimo jiné) a ověřte, že je stejný pro každý požadavek.

## <a name="example-api-request"></a>Ukázkové rozhraní API požadavek

Následuje ukázka požadavku, který obsahuje všechny navrhované parametry a hlavičky dotazu. Pokud voláte některé z rozhraní API Bingu poprvé, nezahrnujte do volání hlavičku ID klienta. ID klienta zahrňte pouze v případě, že jste již dříve volali rozhraní API Bingu a Bing vrátil ID klienta pro příslušnou kombinaci uživatele a zařízení. 
  
> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Následující příklad ukazuje odpověď na předchozí požadavek. Příklad také zobrazuje hlavičky odpovědi specifické pro Bing.

```json
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

# <a name="next-steps"></a>Další postup

- [Co je API kontrola pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
