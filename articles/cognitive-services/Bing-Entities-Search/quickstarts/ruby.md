---
title: 'Rychlý start: Odeslat žádost o vyhledávání Bingu Entity REST API služby Search pomocí Ruby'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete odeslat požadavek na rozhraní Bing Entity REST API pro vyhledávání pomocí Ruby a přijetí odpovědi JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: ed8b590d5f31daebb0cffb270f72ae156acab778
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753142"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Rychlý start pro rozhraní API Bingu pro vyhledávání entit s využitím Ruby

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání entit a zobrazit odpověď JSON. Tato jednoduchá aplikace v Ruby odešle dotaz vyhledávání zpráv na rozhraní API a zobrazí odpovědi. Zdrojový kód pro tuto aplikaci je k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb).

Aplikace je sice napsaná v Ruby, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) nebo novější.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V oblíbeném prostředí IDE nebo editoru kódu vytvořte zpráv Ruby souboru a importujte následující balíčky.

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. Vytváření proměnných pro váš koncový bod rozhraní API, adresa URL pro hledání zpráv, váš klíč předplatného a vyhledávací dotaz.
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>Formátování a provedení požadavku rozhraní API

1. Vytvoření řetězce parametry pro vaši žádost připojením vašeho trhu proměnnou `?mkt=` parametru. Zakódovat váš dotaz a připojte ho k `&q=` parametru. Kombinace hostitele rozhraní API, cestu a parametrů pro vaši žádost a přetypovat jako objekt identifikátoru URI.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. Použití proměnné z poslední krok k vytvoření požadavku. Přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví.

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. Odeslat požadavek a tisku resposne

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
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
