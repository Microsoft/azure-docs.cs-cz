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
ms.openlocfilehash: e7207a1d675298779c3523ee93a8169ac0a26e4a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367110"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Odesílání požadavků do rozhraní API pro kontrolu pravopisu Bingu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Pokud chcete zkontrolovat pravopisné a gramatické chyby v textovém řetězci, odešlete požadavek GET do následujícího koncového bodu:  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
Tento požadavek musí používat protokol HTTPS.

Doporučujeme, aby všechny požadavky pocházely ze serveru. Distribuce klíče v rámci klientské aplikace poskytuje škodlivým třetím stranám víc příležitostí získat k ní přístup. Server také poskytuje jeden bod upgradu pro budoucí verze rozhraní API.

Požadavek musí obsahovat parametr dotazu [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text), který obsahuje textový řetězec ke kontrole. Požadavek by měl obsahovat taky parametr [mkt](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt) dotazu, který identifikuje trh, ze kterého chcete obdržet výsledky, i když je tento parametr volitelný. Seznam volitelných parametrů dotazu, jako `mode`, naleznete v tématu [Parametry dotazu](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters). Všechny hodnoty parametru dotazu musí mít kódování URL.  
  
Požadavek musí obsahovat hlavičku [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey). I když volitelné, doporučujeme také zadat následující hlavičky. Tyto hlavičky pomůžou rozhraní API Bingu pro kontrolu pravopisu vrátit přesnější výsledky:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

Seznam všech hlaviček žádostí a odpovědí najdete v části s [hlavičkami](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers).

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
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)