---
title: 'Rychlý Start: odeslání žádosti o vyhledávání do REST API pomocí jazyka C# – Vyhledávání entit Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odeslat žádost Vyhledávání entit Bingu REST API pomocí jazyka C# a přijmout odpověď JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a0a77faba971f328c6ae1c5a03f8faf7ccfcf60d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351482"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Rychlý Start: odeslání žádosti o vyhledávání do Vyhledávání entit Bingu REST API pomocí jazyka C #

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Tento rychlý Start použijte k provedení prvního volání rozhraní API Bingu pro vyhledávání entit a zobrazení odpovědi JSON. Tato jednoduchá aplikace v jazyce C# pošle do rozhraní API dotaz pro hledání zpráv a zobrazí odpověď. Zdrojový kód této aplikace je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

I když je tato aplikace napsaná v jazyce C#, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.


## <a name="prerequisites"></a>Požadavky

- Libovolná edice sady [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/).
- Nebo pokud používáte Linux nebo MacOS, můžete postupovat podle tohoto rychlého startu pomocí [Visual Studio Code](https://code.visualstudio.com/) a [.NET Core](/dotnet/core/install/macos) .
- [Bezplatný účet Azure](https://azure.microsoft.com/free/dotnet)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nové řešení konzoly C# v aplikaci Visual Studio. 
1. Přidejte [Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/) do balíčku NuGet.
    1. V **Průzkumník řešení** klikněte pravým tlačítkem na svůj projekt.
    2. Vyberte **Spravovat balíčky NuGet**.
    3. Vyhledejte a vyberte *Newtonsoft.Jsna* a pak balíček nainstalujte.
1. Pak přidejte následující obory názvů do hlavního souboru kódu:
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Vytvořte novou třídu a přidejte proměnné pro koncový bod rozhraní API, klíč předplatného a dotaz, který chcete vyhledat. Můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

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

## <a name="send-a-request-and-get-the-api-response"></a>Poslat žádost a získat odpověď rozhraní API

1. V rámci třídy vytvořte funkci s názvem `Search()` . V rámci této funkce vytvořte nový `HttpClient` objekt a přidejte do záhlaví klíč předplatného `Ocp-Apim-Subscription-Key` .

2. Vytvořte identifikátor URI pro vaši žádost kombinací hostitele a cesty. Pak přidejte svůj svůj trh a adresu URL – zakódování dotazu.

3. Očekává `client.GetAsync()` se, že se získá odpověď HTTP, a pak se odpověď JSON uloží podle očekávání `ReadAsStringAsync()` .

4. Naformátujte řetězec JSON pomocí `JsonConvert.DeserializeObject()` a vytiskněte ho do konzoly.

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

5. V `Main()` metodě vaší aplikace zavolejte `Search()` funkci.
    
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
> [Sestavení jednostránkové webové aplikace](../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání entit?](../overview.md )
* [Odkaz na rozhraní API Bingu pro vyhledávání entit](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).