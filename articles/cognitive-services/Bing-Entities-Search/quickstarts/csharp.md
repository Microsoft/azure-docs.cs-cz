---
title: 'Úvodní příručka: Odeslání požadavku na hledání do rozhraní REST API pomocí jazyka C# – vyhledávání entit Bingu'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start slouží k odeslání požadavku do rozhraní REST ROZHRANÍ REST search entity Bing pomocí jazyka C# a obdržíte odpověď JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: c343c160f67eda2dd390ffc39f3b4f1ff49cacb6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448670"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Úvodní příručka: Odeslání požadavku na vyhledávání do rozhraní REST API pro vyhledávání entit Bingu pomocí c #

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní API pro vyhledávání entit Bingu a zobrazit odpověď JSON. Tato jednoduchá aplikace jazyka C# odešle dotaz na vyhledávání zpráv do rozhraní API a zobrazí odpověď. Zdrojový kód pro tuto aplikaci je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Aplikace je sice napsaná v C#, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.


## <a name="prerequisites"></a>Požadavky

- Libovolná edice [Visual Studia 2017 nebo novější](https://www.visualstudio.com/downloads/).

- Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet. Instalace balíčku NuGet v sadě Visual Studio:

   1. Klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení**.
   2. Vyberte **spravovat nugetové balíčky**.
   3. Vyhledejte *Newtonsoft.Json* a nainstalujte balíček.

- Pokud používáte Linux/MacOS, lze tuto aplikaci spustit pomocí [mono](https://www.mono-project.com/).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. vytvořte nové řešení konzoly C# v sadě Visual Studio. Pak přidejte následující obory názvů do souboru hlavního kódu.
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Vytvořte novou třídu a přidejte proměnné pro koncový bod rozhraní API, klíč předplatného a dotaz, který chcete prohledat. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

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

## <a name="send-a-request-and-get-the-api-response"></a>Odeslání požadavku a získání odpovědi rozhraní API

1. V rámci třídy vytvořte funkci nazvanou `Search()`. Vytvořte `HttpClient` nový objekt a přidejte `Ocp-Apim-Subscription-Key` klíč předplatného do záhlaví.

   1. Vytvořte identifikátor URI pro váš požadavek kombinací hostitele a cesty. Pak přidejte svůj trh a URL kódovat dotaz.
   2. Počkejte `client.GetAsync()` na získání odpovědi HTTP a potom uložte odpověď `ReadAsStringAsync()`json čekáním .
   3. Naformátujte řetězec JSON `JsonConvert.DeserializeObject()` a vytiskněte jej do konzoly.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

2. V hlavní metodě aplikace volejte `Search()` funkci.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
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

* [Co je rozhraní API pro vyhledávání entit Bingu?](../overview.md )
* [Odkaz na rozhraní API pro vyhledávání entit Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
