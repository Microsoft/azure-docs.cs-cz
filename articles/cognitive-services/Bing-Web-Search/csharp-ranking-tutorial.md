---
title: Zobrazení výsledků hledání pomocí pořadí
titleSuffix: Azure Cognitive Services
description: Ukazuje, jak použít odpověď Bing RankingResponse k zobrazení výsledků hledání v pořadí.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 1c8e0bb136fddeb84dc991e63a761378b38cc470
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382323"
---
# <a name="build-a-console-app-search-client-in-c"></a>Vytvoření klienta pro vyhledávání konzolových aplikací v C #

Tento kurz ukazuje, jak vytvořit jednoduchou konzolovou aplikaci .NET Core, která uživatelům umožňuje dotazovat se na rozhraní API pro vyhledávání na webu Bing a zobrazit seřazené výsledky.

Tento výukový program ukazuje, jak:

- Vytvoření jednoduchého dotazu do rozhraní API pro vyhledávání na webu Bingu
- Zobrazit výsledky dotazu v seřazeném pořadí

## <a name="prerequisites"></a>Požadavky

Chcete-li sledovat spolu s tutoriálu, musíte:

- Visual Studio. Pokud ji nemáte, [stáhněte si a nainstalujte bezplatnou verzi Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
- Klíč předplatného rozhraní API pro vyhledávání na webu Bingu. Pokud ho nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Vytvoření nového projektu konzolové aplikace

V sadě Visual Studio vytvořte projekt pomocí `Ctrl`+`Shift`+`N`.

V dialogovém okně **Nový projekt** klepněte na **položku Visual C# > aplikace Windows Classic Desktop > Console (.NET Framework).**

Pojmenujte aplikaci **MyConsoleSearchApp**a klepněte na tlačítko **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Přidání balíčku JSON.net Nuget do projektu

JSON.net umožňuje pracovat s odpověďmi JSON vrácenými rozhraním API. Přidejte jeho balíček NuGet do projektu:

- V **Průzkumníku řešení** klikněte pravým tlačítkem myši na projekt a vyberte **spravovat balíčky NuGet...**.
- Na kartě **Procházet** vyhledejte . `Newtonsoft.Json` Vyberte nejnovější verzi a klepněte na tlačítko **Instalovat**.
- Klepněte na tlačítko **OK** v okně **Zkontrolovat změny.**
- Zavřete kartu Visual Studio s názvem **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Přidání odkazu na soubor System.Web

Tento kurz závisí `System.Web` na sestavení. Přidejte odkaz na toto sestavení do projektu:

- V **Průzkumníku řešení**klikněte pravým tlačítkem myši na **Reference** a vyberte **Přidat odkaz...**
- Vyberte **sestavení > frameworku**, potom posuňte dolů a zkontrolujte **Soubor System.Web.**
- Vybrat **OK**

## <a name="add-some-necessary-using-statements"></a>Přidat některé nezbytné příkazy using

Kód v tomto kurzu vyžaduje tři další using příkazy. Přidejte tyto příkazy pod existující `using` příkazy v horní části **Program.cs**:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Požádat uživatele o dotaz

V **Průzkumníku řešení**otevřete **Program.cs**. Aktualizujte `Main()` metodu:

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

- Požádá uživatele o dotaz
- Volání `RunQueryAndDisplayResults(userQuery)` k provedení dotazu a zobrazení výsledků
- Čeká na vstup uživatele, aby se zabránilo okamžitému zavření okna konzoly.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Hledání výsledků dotazu pomocí rozhraní API pro vyhledávání na webu Bingu

Dále přidejte metodu, která se dotazuje na rozhraní API a zobrazí výsledky:

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

- Vytvoří `HttpClient` dotaz na rozhraní API pro vyhledávání na webu.
- Nastaví `Ocp-Apim-Subscription-Key` hlavičku HTTP, kterou Bing používá k ověření požadavku.
- Provede požadavek a použije JSON.net k rekonstrukci výsledků.
- Volání `DisplayAllRankedResults(responseObjects)` pro zobrazení všech výsledků v seřazeném pořadí

Nezapomeňte nastavit hodnotu `Ocp-Apim-Subscription-Key` klíče předplatného.

## <a name="display-ranked-results"></a>Zobrazit seřazené výsledky

Než se ukáže, jak zobrazit výsledky v seřazeném pořadí, podívejte se na ukázkovou odpověď vyhledávání na webu:

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

Objekt `rankingResponse` JSON ([dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) popisuje příslušné pořadí zobrazení pro výsledky hledání. Obsahuje jednu nebo více z následujících skupin s prioritou:

- `pole`: Výsledky hledání pro získání nejviditelnější léčby (například zobrazené nad hlavní čárou a postranním panelem).
- `mainline`: Výsledky hledání, které se zobrazí na hlavní spojce.
- `sidebar`: Výsledky hledání, které se zobrazí na postranním panelu. Pokud není k dispozici postranní panel, zobrazte výsledky pod hlavní čárou.

Odpověď hodnocení JSON může zahrnovat jednu nebo více skupin.

V **Program.cs**přidejte následující metodu, která zobrazí výsledky ve správně seřazeném pořadí:

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

- Smyčky přes `rankingResponse` skupiny, které odpověď obsahuje
- Zobrazí položky v každé skupině voláním`DisplaySpecificResults(...)`

V **Program.cs**přidejte následující dvě metody:

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

Tyto metody spolupracují na výstupu výsledků hledání do konzoly.

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

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [použití hodnocení k zobrazení výsledků](rank-results.md).
