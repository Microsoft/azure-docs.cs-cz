---
title: 'Rychlý start: Odeslat žádost o vyhledávání Bingu Entity Search REST API pomocí Pythonu'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete odeslat požadavek na rozhraní Bing Entity REST API pro vyhledávání pomocí Pythonu a přijetí odpovědi JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: df78c6930552865db9fb25df8e412e8644c8f265
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754706"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-python"></a>Rychlý start: Odeslat žádost o vyhledávání Bingu Entity Search REST API pomocí Pythonu

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání entit a zobrazit odpověď JSON. Tato jednoduchá aplikace Python odešle dotaz vyhledávání zpráv na rozhraní API a zobrazí odpovědi. Zdrojový kód k této ukázce je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py).

Aplikace je sice napsaná v Pythonu, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* [Python](https://www.python.org/downloads/) 2.x nebo 3.x

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve vašich oblíbených prostředím IDE nebo editorem a přidejte následující importy. Vytváření proměnných pro váš klíč předplatného, koncový bod, trhu a vyhledávací dotaz. Vyhledání vašeho koncového bodu na řídicím panelu Azure.

    ```python
    import http.client, urllib.parse
    import json
    
    subscriptionKey = 'ENTER YOUR KEY HERE'
    host = 'api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

2. Vytvořit žádost o adresu url připojením vašeho trhu proměnnou `?mkt=` parametru. Kódování URL dotazu a umožňuje připojení `&q=` parametru. 
    
    ```python
    params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)
    ```

## <a name="send-a-request-and-get-a-response"></a>Odešle žádost a získejte odpověď

1. Vytvořit funkci s názvem `get_suggestions()`. Pak proveďte následující kroky.
    1. Klíč předplatného. přidejte do slovníku s `Ocp-Apim-Subscription-Key` jako klíč.
    2. Použití `http.client.HTTPSConnection()` k vytvoření objektu klienta HTTPS. Odeslat `GET` požadovat pomocí `request()` s informacemi o cestu a parametry a záhlaví.
    3. Odpověď se Store `getresponse()`a vrátí `response.read()`.

    ```python
    def get_suggestions ():
        headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
        conn = http.client.HTTPSConnection (host)
        conn.request ("GET", path + params, None, headers)
        response = conn.getresponse ()
        return response.read()
    ```

2. Volání `get_suggestions()`a tisku odpověď json.

    ```python
    result = get_suggestions ()
    print (json.dumps(json.loads(result), indent=4))
    ```

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

* [Co je API pro vyhledávání entit Bingu](../search-the-web.md)
* [Reference k rozhraní API vyhledávání entit Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
