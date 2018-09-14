---
title: 'Rychlý start: Použití Node.js k volání rozhraní API Bingu pro vyhledávání na webu'
description: V tomto rychlém startu poprvé zavoláte rozhraní API Bingu pro vyhledávání na webu pomocí Node.js a dostanete odpověď ve formátu JSON.
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: erhopf
ms.openlocfilehash: 7a46500f7cbf319c788761bccfaa92197ef67490
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886927"
---
# <a name="quickstart-use-nodejs-to-call-the-bing-web-search-api"></a>Rychlý start: Použití Node.js k volání rozhraní API Bingu pro vyhledávání na webu  

V tomto rychlém startu poprvé zavoláte rozhraní API Bingu pro vyhledávání na webu a dostanete odpověď JSON, a nezabere vám to ani 10 minut.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Požadavky

Tady je pár věcí, které budete potřebovat na začátku tohoto rychlého startu:

* [Node.js 6](https://nodejs.org/en/download/) nebo novější
* Klíč předplatného  

## <a name="create-a-project-and-declare-required-modules"></a>Vytvoření projektu a deklarace požadovaných modulů

Ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Node.js. Pak do svého projektu zkopírujte následující fragment kódu. V tomto rychlém startu se používá striktní režim, který k odesílání a přijímání dat vyžaduje modul `https`.

```javascript
// Use strict mode.
'use strict';

// Require the https module.
let https = require('https');
```

## <a name="define-variables"></a>Definování proměnných

Abychom mohli pokračovat, musíme nastavit několik proměnných. Ověřte, že hodnoty `host` a `path` jsou platné a nahraďte hodnotu `subscriptionKey` platným klíčem předplatného ze svého účtu Azure. Vyhledávací dotaz můžete přizpůsobit. Stačí místo `term` zadat jinou hodnotu.

```javascript
// Replace with a valid subscription key.
let subscriptionKey = 'enter key here';

/*
 * Verify the endpoint URI. If you
 * encounter unexpected authorization errors, double-check this host against
 * the endpoint for your Bing Web search instance in your Azure dashboard.  
 */
let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/search';
let term = 'Microsoft Cognitive Services';

// Validate the subscription key.
if (subscriptionKey.length === 32) {
    bing_web_search(term);
} else {
    console.log('Invalid Bing Search API subscription key!');
    console.log('Please paste yours into the source code.');
}
```

## <a name="create-a-response-handler"></a>Vytvoření obslužné rutiny odpovědi

Vytvořte obslužnou rutinu, která odpověď převede na text a parsuje ji. Funkce `response_handler` se volá při každém požadavku do rozhraní API Bingu pro vyhledávání na webu. Uvidíte to v další části.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        console.log('\nRelevant Headers:\n');
        for (var header in response.headers)
            // Headers are lowercased by Node.js.
            if (header.startsWith("bingapis-") || header.startsWith("x-msedge-"))
                 console.log(header + ": " + response.headers[header]);
        // Stringify and parse the response body.
        body = JSON.stringify(JSON.parse(body), null, '  ');
        console.log('\nJSON Response:\n');
        console.log(body);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};
```

## <a name="make-a-request-and-print-the-response"></a>Vytvoření požadavku a tisk odpovědi

Vytvořte požadavek a volejte rozhraní API Bingu pro vyhledávání na webu. Po vytvoření požadavku se volá funkce `response_handler` a vytiskne se odpověď.

```javascript
let bing_web_search = function (search) {
    console.log('Searching the Web for: ' + term);
        // Declare the method, hostname, path, and headers.
        let request_params = {
            method : 'GET',
            hostname : host,
            path : path + '?q=' + encodeURIComponent(search),
            headers : {
                'Ocp-Apim-Subscription-Key' : subscriptionKey,
            }
        };
    // Request to the Bing Web Search API.
    let req = https.request(request_params, response_handler);
    req.end();
}
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je spuštění kódu. Pokud chcete porovnat svůj kód s naším, najdete [ukázkový kód na GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="sample-response"></a>Ukázková odpověď

Odpovědi rozhraní API Bingu pro vyhledávání na webu se vrátí jako objekt JSON. Ukázková odpověď je zkrácená, aby zobrazovala jenom jeden výsledek.  

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face API",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face API from Microsoft Azure. ... Cognitive Services; Face API;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/en-us/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové vyhledávání Bingu – kurz jednostránkové aplikace](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
