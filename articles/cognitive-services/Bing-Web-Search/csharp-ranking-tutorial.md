---
title: Použití pořadí pro zobrazení výsledků hledání
titleSuffix: Azure Cognitive Services
description: Ukazuje, jak použít odpovědí Bing RankingResponse k zobrazení výsledků hledání v pořadí řazení.
services: cognitive-services
author: bradumbaugh
manager: cgronlun
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: conceptual
ms.date: 05/08/2017
ms.author: brumbaug
ms.openlocfilehash: 3e55830fcfdbea91581a75fcfc343fd522485c5a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123398"
---
# <a name="build-a-console-app-search-client-in-c"></a>Vytvoření konzolového klienta vyhledávací aplikaci v jazyce C#

Tento kurz ukazuje, jak vytvořit jednoduchou aplikaci konzoly .NET Core, který umožňuje uživatelům k dotazování webové API Bingu pro vyhledávání a zobrazení seřazený výsledky.

Tento kurz ukazuje, jak:

- Provést jednoduchý dotaz rozhraní API webové vyhledávání Bingu
- Zobrazit výsledky dotazu v seřazený pořadí

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat v kurzu, budete potřebovat:

- Visual Studio. Pokud nemáte k dispozici, [stáhnout a nainstalovat na bezplatné Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
- Klíč předplatného pro rozhraní API webové vyhledávání Bingu. Pokud ho nemáte, [zaregistrujte si bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Vytvořte nový projekt konzolové aplikace

V sadě Visual Studio vytvořte projekt pomocí `Ctrl`+`Shift`+`N`.

V **nový projekt** dialogového okna, klikněte na tlačítko **Visual C# > klasická plocha Windows > Konzolová aplikace (.NET Framework)**.

Pojmenujte aplikaci **MyConsoleSearchApp**a potom klikněte na tlačítko **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Přidejte do projektu balíček JSON.net Nuget

JSON.net umožňuje pracovat s odpověďmi ve formátu JSON vrácená rozhraním API. Přidejte svůj balíček NuGet do projektu:

- V **Průzkumníka řešení** klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet...** .
- Na **Procházet** kartu, vyhledejte `Newtonsoft.Json`. Vyberte nejnovější verzi a potom klikněte na tlačítko **nainstalovat**.
- Klikněte na tlačítko **OK** tlačítko **změny vyplývající z revize** okna.
- Zavřete Visual Studio karta s názvem **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Přidejte odkaz na System.Web

Tento kurz se spoléhá na `System.Web` sestavení. Do projektu přidejte odkaz na toto sestavení:

- V **Průzkumníka řešení**, klikněte pravým tlačítkem na **odkazy** a vyberte **přidat odkaz...**
- Vyberte **sestavení > Framework**, posuňte se dolů a kontrola **System.Web**
- Vyberte **OK**.

## <a name="add-some-necessary-using-statements"></a>Přidat některé potřebné příkazy using

Kód v tomto kurzu vyžaduje tři další příkazy using. Přidat tyto příkazy pod řádek `using` příkazů v horní části **Program.cs**:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Požádat uživatele, pro dotaz

V **Průzkumníka řešení**, otevřete **Program.cs**. Aktualizace `Main()` metody:

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

Tuto metodu:

- Vyzve uživatele k zadání dotazu
- Volání `RunQueryAndDisplayResults(userQuery)` spustit dotaz a zobrazit výsledky
- Aby se zabránilo okamžitě zavření okna konzoly čeká na vstup uživatele.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Vyhledání výsledků dotazu pomocí rozhraní API webové vyhledávání Bingu

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

Tuto metodu:

- Vytvoří `HttpClient` k dotazování webové rozhraní API hledání
- Nastaví `Ocp-Apim-Subscription-Key` záhlaví HTTP, které Bing používá k ověření požadavku
- Zpracuje požadavek a používá technologii JSON.net k deserializaci výsledků
- Volání `DisplayAllRankedResults(responseObjects)` k zobrazení všech výsledků seřazený podle

Nezapomeňte nastavit hodnotu `Ocp-Apim-Subscription-Key` pro váš klíč předplatného.

## <a name="display-ranked-results"></a>Zobrazení seřazených výsledků

Před zobrazením jak zobrazit výsledky podle seřazený, podívejte se na ukázkové webové vyhledávání odpovědi:

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

`rankingResponse` JSON – objekt ([dokumentaci](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)) popisuje pořadí odpovídající zobrazení pro výsledky hledání. Zahrnuje jednu nebo více skupin následující, seřazený podle priority:

- `pole`: Výsledky hledání získat nejviditelnější zpracování (například zobrazený nad hlavní linie a boční panel).
- `mainline`: Výsledky hledání pro zobrazení v hlavní linii.
- `sidebar`: Výsledky hledání pro zobrazení na bočním panelu. Pokud není k dispozici žádné postranní panel, zobrazte výsledky pod hlavní linie.

Hodnocení odpověď JSON může obsahovat jednu nebo více skupin.

V **Program.cs**, přidejte následující metodu a zobrazte výsledky správně seřazený podle:

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

Tuto metodu:

- Cyklickému `rankingResponse` skupin, které obsahuje odpověď
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

Tyto metody společně k vypsání výsledků vyhledávání do konzoly.

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci. Výstup by měl vypadat nějak takto:

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

Další informace o [pomocí řazení a zobrazte výsledky](rank-results.md).
