---
title: 'Rychlý Start: odeslání požadavku na hledání do REST API pomocí Node. js – Vyhledávání entit Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odeslat žádost Vyhledávání entit Bingu REST API pomocí C#a přijmout odpověď JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 48d2b36e35a2e0b41b1202beda9944339dc7530c
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327120"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Rychlý Start: odeslání žádosti o vyhledávání do Vyhledávání entit Bingu REST API pomocí Node. js

Tento rychlý Start použijte k provedení prvního volání rozhraní API Bingu pro vyhledávání entit a zobrazení odpovědi JSON. Tato jednoduchá aplikace JavaScriptu pošle vyhledávací dotaz na zprávy do rozhraní API a zobrazí odpověď. Zdrojový kód k této ukázce je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

I když je tato aplikace napsaná v JavaScriptu, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Node.js](https://nodejs.org/en/download/).

* [Knihovna požadavků JavaScriptu](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru nový soubor JavaScriptu a nastavte striktnost a požadavky protokolu HTTPS.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Vytvořte proměnné pro koncový bod rozhraní API, klíč předplatného a vyhledávací dotaz.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Přidejte svůj trh a parametry dotazu do řetězce s názvem `query`. Nezapomeňte dotaz zakódovat do URL pomocí `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Zpracování a parsování odpovědi

1. Definujte funkci s názvem `response_handler`, která přebírá volání HTTP, `response`jako parametr. V rámci této funkce proveďte následující kroky:

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

    3. Když je příznak **ukončení** signalizována, analyzujte JSON a vytiskněte ho.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Odeslání požadavku

1. Vytvořte funkci nazvanou `Search` pro odeslání žádosti o vyhledávání. V takovém případě proveďte následující kroky.

   1. Vytvořte objekt JSON, který obsahuje parametry žádosti: pro metodu použijte `Get` a přidejte informace o hostiteli a cestě. Do hlavičky `Ocp-Apim-Subscription-Key` přidejte svůj klíč předplatného. 
   2. Použijte `https.request()` k odeslání požadavku s obslužnou rutinou odpovědi vytvořenou dříve a parametry hledání.
    
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

2. Zavolejte funkci `Search()`.

## <a name="example-json-response"></a>Příklad odpovědi JSON

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
        "url": "https://www.princi.com/",
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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s jednou stránkou](../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání entit?](../overview.md )
* [Odkaz na rozhraní API Bingu pro vyhledávání entit](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
