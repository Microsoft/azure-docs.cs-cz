---
title: 'Rychlý start: Použití sady SDK Bingu pro vyhledávání na webu pro jazyk C#'
titleSuffix: Azure Cognitive Services
description: Sada SDK Bingu pro vyhledávání na webu usnadňuje integraci webového vyhledávání Bingu do vaší aplikace v jazyce C#. V tomto rychlém startu se dozvíte, jak vytvořit instanci klienta, odeslat požadavek a vytisknout odpověď.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: aahi
ms.openlocfilehash: ae536166d75af48ea653b256924b5432cfd1f5ef
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309976"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>Rychlý start: Použití sady SDK Bingu pro vyhledávání na webu pro jazyk C#

Sada SDK Bingu pro vyhledávání na webu usnadňuje integraci webového vyhledávání Bingu do vaší aplikace v jazyce C#. V tomto rychlém startu se dozvíte, jak vytvořit instanci klienta, odeslat požadavek a vytisknout odpověď.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="prerequisites"></a>Požadavky
Tady je pár věcí, které budete na začátku tohoto rychlého startu potřebovat:

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) nebo
* [Visual Studio Code 2017](https://code.visualstudio.com/download)
  * [C# pro Visual Studio Code](https://visualstudio.microsoft.com/downloads/)
  * [Správce balíčků NuGet](https://github.com/jmrog/vscode-nuget-package-manager)
* [Sada .NET Core SDK](https://www.microsoft.com/net/download)

## <a name="create-a-project-and-install-dependencies"></a>Vytvoření projektu a instalace závislostí

> [!TIP]
> Získejte z [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/) nejnovější verzi kódu jako řešení sady Visual Studio.

V prvním kroku vytvoříme nový projekt konzoly. Pokud potřebujete pomoct s nastavením projektu konzoly, přečtěte si článek o vytvoření [prvního programu Hello World v programátorské příručce k jazyku C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program). Pokud chcete ve své aplikaci použít sadu SDK Bingu pro vyhledávání na web, budete si muset nainstalovat `Microsoft.Azure.CognitiveServices.Search.WebSearch` pomocí správce balíčků NuGet.

[Balíček sady SDK webového vyhledávání](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) také nainstaluje:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="declare-dependencies"></a>Deklarace závislostí

Otevřete projekt v sadě Visual Studio nebo v editoru Visual Studio Code a naimportujte tyto závislosti:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
```

## <a name="create-project-scaffolding"></a>Vytvoření projektu pro generování uživatelského rozhraní

Při vytvoření nového projektu konzoly by se také měl pro aplikaci vytvořit obor názvů a třída. Program by měl vypadat takto:

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

V následujících oddílech vytvoříme v této třídě ukázkovou aplikaci.

## <a name="construct-a-request"></a>Vytvoření požadavku

Tento kód vytvoří vyhledávací dotaz.

```csharp
public static void WebResults(WebSearchAPI client)
{
    try
    {
        var webData = client.Web.Search(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>Zpracování odpovědi

Dále přidáme kód, který parsuje odpověď a vytiskne výsledky. Vytiskne se `name` a `url` první webové stránky, obrázku, článku nebo videa, pokud tato položka v objektu odpovědi existuje.

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Deklarace hlavní metody

V této aplikaci obsahuje hlavní metoda kód, který vytvoří instanci klienta, ověří `subscriptionKey` a volá `WebResults`. Než budete pokračovat, ujistěte se, že jste zadali platný klíč předplatného účtu Azure.

```csharp
static void Main(string[] args)
{
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>Spuštění aplikace

Pojďme spustit aplikaci.

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>Definice funkcí a filtrování výsledků

Po prvním volání rozhraní API Bingu pro vyhledávání na webu se pojďme podívat na několik funkcí, které doplňují funkce sady SDK o přesnější dotazy a filtrování výsledků. Každou funkci můžete přidat do aplikace v jazyce C#, kterou jste vytvořili v předchozí části.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Omezení počtu výsledků vrácených Bingem

V této ukázce jsou parametry `count` a `offset` použité k omezení počtu výsledků vrácených na dotaz „Best restaurants in Seattle“. Vytiskne se `name` a `URL` prvního výsledku.

1. Do projektu konzoly přidejte tento kód:
    ```csharp
    public static void WebResultsWithCountAndOffset(WebSearchAPI client)
    {
        try
        {
            var webData = client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20).Result;
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
                var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                if (firstWebPagesResult != null)
                {
                    Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                    Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                    Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first web result!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any Web data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. Přidejte `WebResultsWithCountAndOffset` do `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Spusťte aplikaci.

### <a name="filter-for-news"></a>Filtrování zpráv

V této ukázce použijeme parametr `response_filter` k filtrování výsledků hledání. Vrácené výsledky hledání jsou omezené na zprávy obsahující „Microsoft“. Vytiskne se `name` a `URL` prvního výsledku.

1. Do projektu konzoly přidejte tento kód:
    ```csharp
    public static void WebSearchWithResponseFilter(WebSearchAPI client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings).Result;
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
                var firstNewsResult = webData.News.Value.FirstOrDefault();

                if (firstNewsResult != null)
                {
                    Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                    Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                    Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first News results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any News data..");
            }

        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. Přidejte `WebResultsWithCountAndOffset` do `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Spusťte aplikaci.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Použití bezpečného hledání, počtu odpovědí a propagačního filtru

V této ukázce použijeme parametry `answer_count`, `promote` a `safe_search` k filtrování výsledků hledání pro „Music Videos“. Zobrazí se `name` a `URL` prvního výsledku.

1. Do projektu konzoly přidejte tento kód:
    ```csharp
    public static void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchAPI client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict).Result;
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
                    Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                    Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                }
                else
                {
                    Console.WriteLine("Couldn't find videos results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. Přidejte `WebResultsWithCountAndOffset` do `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Spusťte aplikaci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až projekt dokončíte, nezapomeňte z kódu aplikace odebrat klíč předplatného.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ukázky kognitivních služeb sady Node.js SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)
