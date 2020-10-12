---
title: Odesílání požadavků do rozhraní API pro kontrolu pravopisu Bingu
titleSuffix: Azure Cognitive Services
description: Přečtěte si o režimech Kontrola pravopisu Bingu, nastavení a dalších informacích týkajících se rozhraní API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: de232ab6c52afa45b40ef7863a0cb35d7b40531f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89299112"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Odesílání požadavků do rozhraní API pro kontrolu pravopisu Bingu

Pokud chcete zkontrolovat pravopisné a gramatické chyby v textovém řetězci, odešlete požadavek GET do následujícího koncového bodu:  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
Tento požadavek musí používat protokol HTTPS.

Doporučujeme, aby všechny požadavky pocházely ze serveru. Distribuce klíče v rámci klientské aplikace poskytuje škodlivým třetím stranám víc příležitostí získat k ní přístup. Server také poskytuje jeden bod upgradu pro budoucí verze rozhraní API.

Požadavek musí obsahovat parametr dotazu [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text), který obsahuje textový řetězec ke kontrole. Požadavek by měl obsahovat taky parametr [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt) dotazu, který identifikuje trh, ze kterého chcete obdržet výsledky, i když je tento parametr volitelný. Seznam volitelných parametrů dotazu, jako `mode`, naleznete v tématu [Parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters). Všechny hodnoty parametru dotazu musí mít kódování URL.  
  
Požadavek musí obsahovat hlavičku [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey). I když volitelné, doporučujeme také zadat následující hlavičky. Tyto hlavičky pomůžou rozhraní API Bingu pro kontrolu pravopisu vrátit přesnější výsledky:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

Seznam všech hlaviček žádostí a odpovědí najdete v části s [hlavičkami](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers).

Při volání rozhraní API Bingu pro kontrolu pravopisu pomocí JavaScriptu vám integrované funkce zabezpečení prohlížeče můžou zabránit v přístupu k hodnotám těchto hlaviček.

Pokud chcete tento problém vyřešit, můžete žádost o rozhraní API Bingu pro kontrolu pravopisu vytvořit prostřednictvím serveru proxy CORS. Odpověď z takového proxy serveru obsahuje `Access-Control-Expose-Headers` hlavičku, která filtruje hlavičky odpovědí a zpřístupňuje je pro JavaScript.

Je snadné nainstalovat proxy CORS, aby [aplikace tutorial](../tutorials/spellcheck.md) mohla získat přístup k volitelným hlavičkám klienta. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Pak na příkazovém řádku zadejte následující příkaz.

```console
npm install -g cors-proxy-server
```

Dále změňte koncový bod rozhraní API Bingu pro kontrolu pravopisu v souboru HTML na: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/`

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

```console
cors-proxy-server
```

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V části rozbalit hlavičky HTTP pod výsledky hledání se teď můžete podívat na `X-MSEdge-ClientID` záhlaví (mimo jiné) a ověřit, jestli je pro každý požadavek stejný.

## <a name="example-api-request"></a>Příklad požadavku rozhraní API

Následuje ukázka požadavku, který obsahuje všechny navrhované parametry a hlavičky dotazu. Pokud voláte některé z rozhraní API Bingu poprvé, nezahrnujte do volání hlavičku ID klienta. ID klienta zahrňte pouze v případě, že jste již dříve volali rozhraní API Bingu a Bing vrátil ID klienta pro příslušnou kombinaci uživatele a zařízení. 
  
```http
GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

Následující příklad ukazuje odpověď na předchozí požadavek. Příklad také zobrazuje hlavičky odpovědi specifické pro Bing.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

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

## <a name="next-steps"></a>Další kroky

- [Co je rozhraní API pro kontrolu pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
