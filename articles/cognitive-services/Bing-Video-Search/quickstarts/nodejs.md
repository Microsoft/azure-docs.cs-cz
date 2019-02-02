---
title: 'Rychlý start: Hledání videí pomocí API REST pro vyhledávání videí Bingu a Node.js'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu používáme k odeslání žádosti pro vyhledávání videí do Bingu Video Search REST API prostřednictvím JavaScriptu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: f593863b86bf5170c4d5c19cece622c1dcafc89c
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569504"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-nodejs"></a>Rychlý start: Hledání videí pomocí API REST pro vyhledávání videí Bingu a Node.js

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání Video a zobrazení výsledků na vyhledávacím z odpovědi JSON. Tato jednoduchá aplikace JavaScript odešle dotaz pro vyhledávání videí HTTP do rozhraní API a zobrazí odpovědi. Zatímco tato aplikace je napsána v jazyce JavaScript a Node.js používá, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků. Zdrojový kód této ukázky je dostupný na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingVideoSearchv7.js) s dalším ošetřením chyb a poznámkami ke kódu.

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/download/)

* Modul požadavku pro JavaScript
    * Můžete nainstalovat pomocí tohoto modulu `npm install request`

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializace aplikace

1. Vytvořte nový soubor jazyka JavaScript v Oblíbené prostředí IDE nebo editoru. Nastavte přísnosti a přidejte následující požadavky:

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Vytváření proměnných pro váš koncový bod rozhraní API, klíč předplatného a hledaný termín.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/videos/search';
    let term = 'kittens';
    ```

## <a name="create-a-response-handler"></a>Vytvoření obslužné rutiny odpovědi

1. Vytvořit funkci s názvem `response_handler` přijmout odpověď JSON z rozhraní API. Vytvořte proměnnou pro text odpovědi. Přidat odpověď při `data` přijatých příznak pomocí `response.on()`.

    ```javascript
    let response_handler = function (response) {
        let body = '';
        response.on('data', function (d) {
            body += d;
        });
    };
    ```
    
    2. Když `end` je signalizován, použijte `response.on()` k uložení Bingu související záhlaví (počínaje `bingapis` nebo `x-msedge-`). Potom parsovat JSON pomocí `JSON.parse()`, převeďte jej na řetězec s `JSON.stringify()`a vytisknout si ho.

        ```javascript
        response.on('end', function () {
            for (var header in response.headers)
                // header keys are lower-cased by Node.js
                if (header.startsWith("bingapis-") || header.startsWith("x-msedge-"))
                     console.log(header + ": " + response.headers[header]);
            body = JSON.stringify(JSON.parse(body), null, '  ');
            //JSON Response body
            console.log(body);
        });
        ```

# <a name="create-and-send-the-search-request"></a>Vytvoření a odeslání požadavku hledání

1. Vytvořit funkci s názvem `bing_video_search()`. Přidáte parametry pro vaši žádost, včetně názvu hostitele a záhlaví. Kódování hledaný termín a přidejte je do vašeho parametr cesty s `?q=` parametru. Odešlete požadavek s `req.end()`.

    ```javascript
    let bing_video_search = function (search_term) {
      console.log('Searching videos for: ' + term);
      let request_params = {
            method : 'GET',
            hostname : host,
            path : path + '?q=' + encodeURIComponent(search_term),
            headers : {
                'Ocp-Apim-Subscription-Key' : subscriptionKey,
            }
        };
        let req = https.request(request_params, response_handler);
        req.end();
    }
    ```

## <a name="json-response"></a>Odpověď JSON

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové jednostránkové aplikace](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

 [Co je API pro vyhledávání videí Bingu?](../overview.md)
