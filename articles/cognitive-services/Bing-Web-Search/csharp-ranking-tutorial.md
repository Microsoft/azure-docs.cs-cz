---
title: Použití pořadí k zobrazení výsledků hledání
titleSuffix: Azure Cognitive Services
description: Ukazuje, jak použít odpověď RankingResponse Bingu k zobrazení výsledků hledání v pořadí řazení.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 9131dbff9b732ecfc7f6edb62b42959abcc17da8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078675"
---
# <a name="build-a-console-app-search-client-in-c"></a>Sestavení klienta hledání konzolové aplikace v C #

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto kurzu se dozvíte, jak vytvořit jednoduchou konzolovou aplikaci .NET Core, která umožňuje uživatelům dotazovat se na rozhraní API Bingu pro vyhledávání na webu a zobrazovat seřazené výsledky.

V tomto kurzu se dozvíte, jak:

- Vytvoření jednoduchého dotazu na rozhraní API Bingu pro vyhledávání na webu
- Zobrazit výsledky dotazu v pořadí podle pořadí

## <a name="prerequisites"></a>Předpoklady

Abyste mohli postupovat podle tohoto kurzu, budete potřebovat:

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" vytvořte prostředek vyhledávání Bingu vytvoření prostředku "  target="_blank"> Vyhledávání Bingu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku** .
* [Integrované vývojové prostředí sady Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="create-a-new-console-app-project"></a>Vytvořit nový projekt konzolové aplikace

V sadě Visual Studio vytvořte projekt pomocí `Ctrl`+`Shift`+`N`.

V dialogovém okně **Nový projekt** klikněte na **Visual C# > Windows Classic Desktop > Konzolová aplikace (.NET Framework)** .

Pojmenujte aplikaci **MyConsoleSearchApp** a pak klikněte na **OK** .

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Přidat do projektu balíček NuGet JSON.net

JSON.net umožňuje pracovat se odpověďmi JSON vrácenými rozhraním API. Přidejte svůj balíček NuGet do projektu:

- V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet...** .
- Na kartě  **Procházet** vyhledejte `Newtonsoft.Json` . Vyberte nejnovější verzi a pak klikněte na **nainstalovat** .
- V okně **Zkontrolovat změny** klikněte na tlačítko **OK** .
- Zavřete kartu sady Visual Studio s názvem **NuGet: MyConsoleSearchApp** .

## <a name="add-a-reference-to-systemweb"></a>Přidat odkaz na System. Web

Tento kurz spoléhá na `System.Web` sestavení. Přidejte do projektu odkaz na toto sestavení:

- V **Průzkumník řešení** klikněte pravým tlačítkem na **odkazy** a vyberte **Přidat odkaz...**
- Vyberte **sestavení > Framework** a pak se posuňte dolů a zkontrolujte **System. Web.**
- Vybrat **OK**

## <a name="add-some-necessary-using-statements"></a>Přidat některé nezbytné příkazy using

Kód v tomto kurzu vyžaduje tři další příkazy using. Přidejte tyto příkazy pod stávající `using` příkazy v horní části **program.cs** :

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Požádat uživatele o dotaz

V **Průzkumník řešení** otevřete **program.cs** . Aktualizujte `Main()` metodu:

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

- Vyzve uživatele k zadání dotazu.
- Volání `RunQueryAndDisplayResults(userQuery)` pro spuštění dotazu a zobrazení výsledků
- Čeká na vstup uživatele, aby nedocházelo k okamžitému zavření okna konzoly.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Hledání výsledků dotazu pomocí rozhraní API Bingu pro vyhledávání na webu

Dále přidejte metodu, která bude dotazovat rozhraní API a zobrazí výsledky:

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

- Vytvoří `HttpClient` dotaz na rozhraní vyhledávání na webu API.
- Nastaví `Ocp-Apim-Subscription-Key` hlavičku protokolu HTTP, kterou Bing používá k ověření žádosti.
- Provede požadavek a použije JSON.net k deserializaci výsledků.
- Volání `DisplayAllRankedResults(responseObjects)` pro zobrazení všech výsledků v pořadí podle pořadí

Ujistěte se, že jste nastavili hodnotu `Ocp-Apim-Subscription-Key` na klíč předplatného.

## <a name="display-ranked-results"></a>Zobrazit seřazené výsledky

Než se pustíte do zobrazení výsledků v pořadí podle pořadí, podívejte se na ukázkovou odpověď na vyhledávání na webu:

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

`rankingResponse`Objekt JSON ([dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) popisuje odpovídající pořadí zobrazení výsledků hledání. Zahrnuje jednu nebo více následujících možností s určením prioritních skupin:

- `pole`: Výsledky hledání pro získání nejužitečnějších úprav (například zobrazené nad hlavní a postranní panel).
- `mainline`: Výsledky hledání, které se mají zobrazit v hlavní
- `sidebar`: Výsledky hledání, které se mají zobrazit na bočním panelu. Pokud není k dispozici žádný postranní panel, zobrazte výsledky pod hlavní.

JSON odpovědi na řazení může zahrnovat jednu nebo více skupin.

V **program.cs** přidejte následující metodu pro zobrazení výsledků v pořadí podle správné klasifikace:

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

- Projde mezi `rankingResponse` skupinami, které odpověď obsahuje.
- Zobrazí položky v každé skupině voláním `DisplaySpecificResults(...)`

Do **program.cs** přidejte následující dvě metody:

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
