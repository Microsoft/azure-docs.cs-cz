---
title: 'Rychlý start: Odeslat žádost o vyhledávání Bingu Entity Search REST API pomocí Node.js'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete odeslat požadavek na pomocí API REST pro vyhledávání entit Bingu C#a získejte odpověď ve formátu JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 87afdd9e949ec866c6ee962c2b68af42590bee67
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878710"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Rychlý start: Odeslat žádost o vyhledávání Bingu Entity Search REST API pomocí Node.js

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání entit a zobrazit odpověď JSON. Tato jednoduchá aplikace JavaScript odešle dotaz vyhledávání zpráv na rozhraní API a zobrazí odpovědi. Zdrojový kód k této ukázce je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Zatímco tato aplikace je napsána v jazyce JavaScript, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Node.js](https://nodejs.org/en/download/).

* [Knihovna žádostí o jazyka JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru nový soubor JavaScriptu a nastavte striktnost a požadavky protokolu HTTPS.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Vytváření proměnných pro koncový bod rozhraní API, klíč předplatného a vyhledávací dotaz.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Přidat vstup na trh a dotaz parametry řetězec s názvem `query`. Nezapomeňte si adresu url – kódování dotazu s `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Zpracování a parsování odpovědi

1. definovat funkci s názvem `response_handler` , která přijímá volání protokolu HTTP, `response`, jako parametr. v rámci této funkce proveďte následující kroky:

    1. Definujte proměnnou, která bude obsahovat text odpovědi JSON.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Uložte text odpovědi při volání příznaku **data**.
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Když **koncové** signalizována příznak, analyzovat ve formátu JSON a vytisknout je.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Odeslání požadavku

1. Vytvořit funkci s názvem `Search` odeslat žádost o vyhledávání. V něm proveďte následující kroky.

    1. Vytvořit objekt JSON, který obsahuje parametry požadavku: použijte `Get` pro metodu a přidejte své informace o hostitele a cestu. Přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví. 
    2. Použití `https.request()` k odeslání žádosti s dříve vytvořenou obslužné rutiny a parametry hledání.
    
    ```javascript
    let Search = function () {
        let request_params = {
            method : 'GET',
            hostname : host,
            path : path + query,
            headers : {
                'Ocp-Apim-Subscription-Key' : subscriptionKey,
            }
        };
    
        let req = https.request (request_params, response_handler);
        req.end ();
    }
    ```

2. Volání `Search()` funkce.

## <a name="example-json-response"></a>Příklad JSON odpovědi

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové jednostránkové aplikace](../tutorial-bing-entities-search-single-page-app.md)

* [Co je API pro vyhledávání entit Bingu?](../overview.md )
* [Reference k rozhraní API vyhledávání entit Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
