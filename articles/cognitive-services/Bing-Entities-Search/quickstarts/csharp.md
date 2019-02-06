---
title: 'Rychlý start: Odeslat žádost o vyhledávání pomocí API REST pro vyhledávání entit BinguC#'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete odeslat požadavek na pomocí API REST pro vyhledávání entit Bingu C#a získejte odpověď ve formátu JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 63890d916f80fbfac7173abd0df9e559bcd0b76b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754927"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Rychlý start: Odeslat žádost o vyhledávání pomocí API REST pro vyhledávání entit BinguC#

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání entit a zobrazit odpověď JSON. Tento jednoduchý C# aplikace odešle zprávy vyhledávací dotaz do rozhraní API a zobrazí odpovědi. Zdrojový kód pro tuto aplikaci je k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Aplikace je sice napsaná v C#, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.


## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet.
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](http://www.mono-project.com/).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nový C# konzole řešení v sadě Visual Studio. Pak přidejte následující obory názvů do souboru hlavního kódu.
    
    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Vytvořte novou třídu a přidejte proměnné pro koncový bod rozhraní API, klíč předplatného a dotaz, který chcete vyhledávat.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Odešle žádost a získejte odpovědi rozhraní API

1. V rámci třídy, vytvořte funkci s názvem `Search()`. Vytvořte nový `HttpClient` objektu a přidat váš klíč předplatného na `Ocp-Apim-Subscription-Key` záhlaví.

    1. Vytvořte identifikátor URI pro vaši žádost kombinací hostitele a cestu. Pak přidejte trhu a kódování URL dotazu.
    2. Operátor await `client.GetAsync()` získat odpověď HTTP, a potom ukládat odpověď json tak, že čekají na `ReadAsStringAsync()`.
    3. Vypsat řetězec do konzoly.

    ```csharp
    async static void Search()
    {
        //...
        HttpClient client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

        HttpResponseMessage response = await client.GetAsync(uri);

        string contentString = await response.Content.ReadAsStringAsync();
        Console.WriteLine(JsonPrettyPrint(contentString));
    }
    ```

2. V hlavní metodě vaší aplikace, zavolejte `Search()` funkce.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
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

* [Co je API pro vyhledávání entit Bingu?](../overview.md )
* [Reference k rozhraní API vyhledávání entit Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
