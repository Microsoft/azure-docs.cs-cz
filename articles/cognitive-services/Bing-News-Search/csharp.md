---
title: 'Rychlý start: Hledání zpráv s C# -REST rozhraní API pro vyhledávání zpráv Bingu'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete odeslat požadavek na rozhraní API REST vyhledávání zpráv Bingu pomocí C#a získejte odpověď ve formátu JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 44958a806d7476bfc347236421758d6f8c4bc5ad
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150706"
---
# <a name="quickstart-search-for-news-using-c-and-the-bing-news-search-rest-api"></a>Rychlý start: Hledání zpráv pomocí C# a rozhraní API REST vyhledávání zpráv Bingu

V tomto rychlém startu můžete provést první volání do rozhraní API pro vyhledávání zpráv Bingu a zobrazit odpověď JSON. Tento jednoduchý C# aplikace odešle zprávy vyhledávací dotaz do rozhraní API a zobrazí odpovědi. Úplný kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingNewsSearchv7.cs).

Aplikace je sice napsaná v C#, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet.
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](http://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nový C# konzole řešení v sadě Visual Studio. Pak přidejte následující obory názvů do souboru hlavního kódu.
    
    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Vytvořte proměnné pro koncový bod rozhraní API, klíč předplatného a hledaný výraz.

    ```csharp
    const string accessKey = "enter key here";
    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/news/search";
    const string searchTerm = "Microsoft";
    ```
## <a name="create-a-struct-to-format-the-bing-news-search-response"></a>Vytvoření struktury formátovat odpověď pro vyhledávání zpráv Bingu

1. Definujte strukturu `SearchResult` tak, aby obsahovala výsledky hledání obrázků, a informace v hlavičce JSON.

    ```csharp
    struct SearchResult
    {
        public String jsonResult;
        public Dictionary<String, String> relevantHeaders;
    }
    ```

## <a name="create-and-handle-a-news-search-request"></a>Vytvořit a zpracovat žádost o vyhledávání zpráv

Vytvořte metodu s názvem `BingNewsSearch`, která provede volání rozhraní API a nastaví typ vrácení na strukturu `SearchResult` vytvořenou dříve. V metodě proveďte následující kroky:

1. Sestavte URI pro žádost o vyhledávání. Všimněte si, že hledaný výraz `toSearch` musí být formátovaný, než se připojí k řetězci.

    ```csharp
    static SearchResult BingNewsSearch(string toSearch){

        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. Proveďte webový požadavek a získejte odpověď jako řetězec JSON.

    ```csharp
    WebRequest request = WebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = subscriptionKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    ```

3. Vytvořte objekt výsledků hledání a extrahujte hlavičky HTTP Bingu. Pak vraťte `searchResult`.

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

3. Vytvořte objekt výsledků hledání a extrahujte hlavičky HTTP Bingu. Pak vraťte `searchResult`.

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

## <a name="process-the-response"></a>Zpracovat odpověď

1. V hlavní metodě volejte `BingNewsSearch()` a uložte vrácenou odpověď. Potom deserializujte JSON na objekt. Pak můžete zobrazit hodnoty odpovědi.

    ```csharp
    SearchResult result = BingNewsSearch(searchTerm);
    //deserialize the JSON response
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result.jsonResult);
    Console.WriteLine(jsonObj["value"][0])
    ```

## <a name="json-response"></a>Odpověď JSON

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .

      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
