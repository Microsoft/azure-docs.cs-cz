---
title: 'Rychlý Start: provedení vyhledávání na webu pomocí Node.js-Vyhledávání na webu Bingu REST API'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odesílat požadavky na Vyhledávání na webu Bingu REST API pomocí Node.js a přijímat odpověď JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: ea78b90719200bd1176b90986b021d44b34e06e1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91298405"
---
# <a name="quickstart-search-the-web-using-the-bing-web-search-rest-api-and-nodejs"></a>Rychlý Start: vyhledávání na webu pomocí Vyhledávání na webu Bingu REST API a Node.js

V tomto rychlém startu můžete provést první volání rozhraní API Bingu pro vyhledávání na webu. Tato Node.js aplikace odešle požadavek na hledání do rozhraní API a zobrazí odpověď JSON. I když je tato aplikace napsaná v JavaScriptu, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Předpoklady

Tady je pár věcí, které budete potřebovat na začátku tohoto rychlého startu:

* [Node.js 6](https://nodejs.org/en/download/) nebo novější
* Klíč předplatného

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-declare-required-modules"></a>Vytvoření projektu a deklarace požadovaných modulů

Ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Node.js. Pak zkopírujte následující fragment kódu do projektu v souboru s názvem search.js:

```javascript
// Use this simple app to query the Bing Web Search API and get a JSON response.
// Usage: node search.js "your query".
const https = require('https')
```

## <a name="set-the-subscription-key"></a>Nastavení klíče předplatného

Tento fragment kódu používá `AZURE_SUBSCRIPTION_KEY` proměnnou prostředí k uložení klíče předplatného, což je dobrý postup k tomu, abyste zabránili nechtěnému vystavení klíčů při nasazování kódu.

Pokud nejste obeznámeni s používáním proměnných prostředí nebo chcete tuto aplikaci spustit co nejrychleji, nahraďte `process.env['AZURE_SUBSCRIPTION_KEY']` pomocí klíče předplatného nastaveného jako řetězec.

```javascript
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('AZURE_SUBSCRIPTION_KEY is not set.')
}
```

## <a name="create-a-function-to-make-the-request"></a>Vytvoření funkce k odeslání požadavku

Tato funkce vytváří požadavek na zabezpečený přístup a ukládá vyhledávací dotaz jako parametr dotazu v cestě. 

1. Pro tuto `hostname` hodnotu můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.  

2. Použijte `encodeURIComponent` k úniku neplatných znaků. Klíč předplatného se předává v hlavičce. 

3. Zpětné volání dostane [odpověď](https://nodejs.org/dist/latest-v10.x/docs/api/http.html#http_class_http_serverresponse), která provede registraci k události `data`, aby se agregoval text JSON, k události `error`, aby se protokolovaly veškeré problémy, a k události `end`, aby se vědělo, kdy se má zpráva považovat za šablonu. 

4. Po dokončení aplikace budou vytištěny příslušné hlavičky a tělo zprávy. Barvy můžete upravit a nastavit hloubku tak, aby vyhovovaly vašemu předvolbě. Hloubka `1` obsahuje dobrý souhrn odpovědi.

```javascript
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
```

## <a name="get-the-query"></a>Získání dotazu

Podívejme se na argumenty programu, abychom našli daný dotaz. První argument je cesta k uzlu, druhý název souboru a třetí je váš dotaz. Pokud dotaz chybí, použije se výchozí dotaz Microsoft Cognitive Services.

```javascript
const query = process.argv[2] || 'Microsoft Cognitive Services'
```

## <a name="make-a-request-and-print-the-response"></a>Vytvoření požadavku a tisk výsledků

Teď, když je všechno definované, můžeme zavolat naši funkci.

```javascript
bingWebSearch(query)
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je spuštění kódu pomocí příkazu: `node search.js "<your query>"` .

Pokud chcete porovnat svůj kód s naším, tady je celý program:

```javascript
const https = require('https')
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('Missing the AZURE_SUBSCRIPTION_KEY environment variable')
}
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
const query = process.argv[2] || 'Microsoft Cognitive Services'
bingWebSearch(query)
```

## <a name="example-json-response"></a>Příklad odpovědi JSON

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
            "name": "Face",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face service from Microsoft Azure. ... Cognitive Services; Face service;"
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
            "url": "https://www.microsoft.com/cognitive-services/emotion-api",
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
> [Kurz rozhraní API Bingu pro vyhledávání na webu jednostránkové aplikace](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
