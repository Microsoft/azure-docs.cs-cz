---
title: 'Úvodní příručka: Odeslání požadavku na vyhledávání do rozhraní REST API pomocí ruby – vyhledávání entit Bingu'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start slouží k odeslání požadavku do rozhraní REST API hledání entit Bingu pomocí ruby a obdržíte odpověď JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 69e4d992e2ef89b4d3d9408d6e50591fb8166c79
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75385775"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Rychlý start pro rozhraní API Bingu pro vyhledávání entit s využitím Ruby

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní API pro vyhledávání entit Bingu a zobrazit odpověď JSON. Tato jednoduchá aplikace Ruby odešle dotaz na vyhledávání zpráv do rozhraní API a zobrazí odpověď. Zdrojový kód pro tuto aplikaci je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb).

Aplikace je sice napsaná v Ruby, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) nebo novější.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Ve svém oblíbeném ide nebo editoru kódu vytvořte soubor Ruby a importujte následující balíčky.

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. Vytvořte proměnné pro koncový bod rozhraní API, adresu URL vyhledávání příspěvků, klíč předplatného a vyhledávací dotaz. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>Formátování a provedení požadavku rozhraní API

1. Vytvořte řetězec parametrů pro váš požadavek připojením `?mkt=` proměnné trhu k parametru. Zakódujte dotaz a přidejte `&q=` jej k parametru. Zkombinujte hostitele rozhraní API, cestu a parametry pro váš požadavek a přetypujte je jako objekt URI.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. K vytvoření požadavku použijte proměnné z posledního kroku. Přidejte klíč předplatného `Ocp-Apim-Subscription-Key` do záhlaví.

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. Odeslat požadavek a vytisknout odpověď

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
    ```

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
> [Vytvoření jednostránkové webové aplikace](../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API pro vyhledávání entit Bingu](../search-the-web.md)
* [Odkaz na rozhraní API pro vyhledávání entit Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
