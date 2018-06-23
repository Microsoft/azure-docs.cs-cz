---
title: Použití pořadí pro zobrazení výsledků vyhledávání | Microsoft Docs
description: Ukazuje, jak používat Bing RankingResponse odpověď pro zobrazení výsledků hledání v rank pořadí.
services: cognitive-services
author: bradumbaugh
manager: bking
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 05/08/2017
ms.author: brumbaug
ms.openlocfilehash: ec47b8448c0c39cc54e4c79434ce7a2d926df341
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342717"
---
# <a name="build-a-console-app-search-client-in-c"></a>Vytvoření konzolového klienta vyhledávání aplikace v jazyce C#

Tento kurz ukazuje, jak vytvořit jednoduchou aplikaci konzoly .NET Core, která umožňuje uživatelům dotaz rozhraní API služby Bing webové Search a zobrazit seřazený výsledky.

Tento kurz ukazuje, jak:

- Provést jednoduchý dotaz rozhraní API služby Bing webové Search
- Zobrazí výsledky dotazu v seřazený pořadí

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kurzu, je třeba:

- Visual Studio. Pokud nemáte, [stáhněte a nainstalujte bezplatná edice Community 2017 Visual Studio](https://www.visualstudio.com/downloads/).
- Klíč předplatného pro rozhraní API pro vyhledávání webové služby Bing. Pokud nemáte, [zaregistrovat k bezplatné zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Vytvořte nový projekt konzolové aplikace

V sadě Visual Studio vytvořte projekt pomocí `Ctrl`+`Shift`+`N`.

V **nový projekt** dialogové okno, klikněte na tlačítko **Visual C# > klasický desktopový Windows > konzolovou aplikaci (rozhraní .NET Framework)**.

Název aplikace **MyConsoleSearchApp**a potom klikněte na **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Do projektu přidejte balíček JSON.net Nuget

JSON.net umožňuje pracovat s JSON odpovědi vrácené rozhraní API. Přidejte svůj balíček NuGet do projektu:

- V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet...** . 
- Na **Procházet** kartě, vyhledejte vyhledávání `Newtonsoft.Json`. Vyberte nejnovější verzi a pak klikněte na tlačítko **nainstalovat**. 
- Klikněte **OK** na tlačítko **zkontrolujte změny** okno.
- Zavřete Visual Studio kartu s názvem **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Přidat odkaz na System.Web

Tento kurz využívá `System.Web` sestavení. Do projektu přidejte odkaz na toto sestavení:

- V **Průzkumníku řešení**, klikněte pravým tlačítkem na **odkazy** a vyberte **přidat odkaz na...**
- Vyberte **sestavení > Framework**, posuňte se dolů a zkontrolujte **System.Web**
- Vyberte **OK**.

## <a name="add-some-necessary-using-statements"></a>Přidat některé potřebné příkazy using

Kód v tomto kurzu vyžaduje tři další příkazy using. Přidejte tyto příkazy pod existující `using` příkazy v horní části **Program.cs**: 

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Výzvy k zadání dotazu

V **Průzkumníku řešení**, otevřete **Program.cs**. Aktualizace `Main()` metoda:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Tato metoda:

- Požádá uživatele o dotazu
- Volání `RunQueryAndDisplayResults(userQuery)` k provedení dotazu a zobrazení výsledků
- Čeká na vstup uživatele, aby se zabránilo okamžitě zavření v okně konzoly.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Hledat výsledky dotazu pomocí rozhraní API služby Bing webové Search

V dalším kroku přidejte metodu, která se dotazuje rozhraní API a zobrazí výsledky:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Tato metoda:

- Vytvoří `HttpClient` dotazovat webového rozhraní API vyhledávání
- Nastaví `Ocp-Apim-Subscription-Key` hlavičky protokolu HTTP, který Bing používá pro ověření žádosti
- Zpracuje požadavek a používá technologii JSON.net k deserializaci výsledků
- Volání `DisplayAllRankedResults(responseObjects)` zobrazit všechny výsledky v seřazený pořadí

Nezapomeňte nastavit hodnotu `Ocp-Apim-Subscription-Key` na svůj klíč předplatného.

## <a name="display-ranked-results"></a>Zobrazit seřazený výsledky

Před zobrazením jak zobrazit výsledky v seřazený pořadí, podívejte se na odpověď ukázkové webové vyhledávání: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...
        
        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...
        
        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

`rankingResponse` Objekt JSON ([dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)) popisuje pořadí odpovídající zobrazení pro výsledky hledání. Zahrnuje jednu nebo více skupin následující, seřazený podle priority: 

- `pole`: Výsledků vyhledávání do získat nejvíce viditelné způsob zpracování (například zobrazen výše nejdůležitějších a bočním panelu).
- `mainline`K zobrazení v nejdůležitějších: výsledky hledání.
- `sidebar`Chcete-li zobrazit na bočním panelu: výsledky hledání. Pokud není žádná bočním panelu, zobrazte výsledky níže nejdůležitějších.

Hodnocení odpovědi JSON může zahrnovat jeden nebo více skupin.

V **Program.cs**, přidejte následující metodu pro zobrazení výsledků v správně seřazený pořadí:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Tato metoda:

- Cyklicky prochází přes `rankingResponse` skupin, které obsahuje odpovědi
- Zobrazí položky v každé skupině voláním `DisplaySpecificResults(...)` 

V **Program.cs**, přidejte následující dvě metody:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Tyto metody spolupracují k vypsání výsledků vyhledávání do konzoly.

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci. Výstup by měl vypadat podobně jako následující:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Další postup

Další informace o [pomocí klasifikace zobrazíte výsledky](rank-results.md).