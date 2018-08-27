---
title: 'Rychlý start: Použití vyhledávání na webu Bingu sady SDK pro jazyk C#'
description: Instalační program pro vyhledávání na webu konzolovou aplikaci C# SDK.
titleSuffix: Azure cognitive services Web search SDK C# quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 08/16/2018
ms.author: v-gedod, erhopf
ms.openlocfilehash: ef54487a1df7303fa92a78e4f3219f40f558da2b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887315"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>Rychlý start: Použití vyhledávání na webu Bingu sady SDK pro jazyk C#

Sady SDK webové vyhledávání Bingu obsahuje funkce rozhraní REST API pro webové požadavky a výsledky analýzy.

[Zdrojový kód pro ukázky jazyka C# Bingu webové vyhledávání SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/BingSearchv7/BingWebSearch/WebSearchSamples.cs) je k dispozici na Githubu.

## <a name="application-dependencies"></a>Závislosti aplikace

Pokud chcete nastavit konzolovou aplikaci pomocí sady SDK webové vyhledávání Bingu, přejděte na `Manage NuGet Packages` možnost z Průzkumníku řešení v sadě Visual Studio.  Přidat `Microsoft.Azure.CognitiveServices.Search.WebSearch` balíčku.

Instalace [balíček NuGet sady SDK hledání webové](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) taky nainstaluje závislosti, včetně:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="web-search-client"></a>Webový klient vyhledávání
Chcete-li vytvořit instanci `WebSearchAPI` klienta, přidání direktivy using:
```
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;

```
Potom vytvořte instanci klienta:
```
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Pomocí klienta na hledání text dotazu:
```
// Search for "Yosemite National Park"
var webData = client.Web.Search(query: "Yosemite National Park");
Console.WriteLine("Searched for Query# \" Yosemite National Park \"");

```
Analyzovat webové stránky, vrátila ve výsledcích předchozího dotazu:
```
//WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results #{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!)";
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```
## <a name="complete-console-application"></a>Kompletní konzolové aplikace

Následující konzolovou aplikaci spustí dříve definovaný dotaz a analyzuje webových stránek, obrázků, zpráv a videa obsažené ve výsledcích:
```
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;

namespace WebSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            WebResults(client);
            // Include the following calls to use queries defined under following headings.
            //WebResultsWithCountAndOffset(client);  
            //WebSearchWithResponseFilter(client);
            //WebSearchWithAnswerCountPromoteAndSafeSearch(client);

            Console.WriteLine("Any key to exit... ");
            Console.ReadKey();
        }

        public static void WebResults(WebSearchAPI client)
        {
            try
            {
                // Search for (Yosemite National Park"), print the number of results and print out name and url of first result.
                var webData = client.Web.Search(query: "Yosemite National Park");
                Console.WriteLine("Searched for Query# \" Yosemite National Park \"");

                //WebPages
                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Webpage Results #{0}", webData.WebPages.Value.Count);
                        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find web results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any Web data..");
                }

                //Images
                if (webData?.Images?.Value?.Count > 0)
                {
                    // find the first image result
                    var firstImageResult = webData.Images.Value.FirstOrDefault();

                    if (firstImageResult != null)
                    {
                        Console.WriteLine("Image Results #{0}", webData.Images.Value.Count);
                        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
                        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find first image results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any image data..");
                }

                //News
                if (webData?.News?.Value?.Count > 0)
                {
                    // find the first news result
                    var firstNewsResult = webData.News.Value.FirstOrDefault();

                    if (firstNewsResult != null)
                    {
                        Console.WriteLine("\r\nNews Results #{0}", webData.News.Value.Count);
                        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find any News results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see first news data..");
                }

                //Videos
                if (webData?.Videos?.Value?.Count > 0)
                {
                    // find the first video result
                    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

                    if (firstVideoResult != null)
                    {
                        Console.WriteLine("\r\nVideo Results #{0}", webData.Videos.Value.Count);
                        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
                        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find first video results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any video data..");
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

        }
    }
}

```

Ukázky vyhledávání Bingu ukazují různé funkce sady SDK.  Přidejte následující funkce na dříve definované `WebSrchSDK` třídy.

## <a name="count-and-offset-parameters"></a>Počet a posun parametry

Následující kód vyhledá "Nejlepší restaurace v Praze", ověřuje počet výsledků a vytiskne název a adresu URL první výsledek.

```
       public static void WebResultsWithCountAndOffset(WebSearchAPI client)
        {
            try
            {
                var webData = client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20).Result;
                Console.WriteLine("\r\nSearched for Query# \" Best restaurants in Seattle \"");

                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
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
## <a name="response-filter"></a>Filtr odpovědí

Následující dotaz hledá výraz "Microsoft" pomocí filtru odpovědi nastavena na `news` a potom zobrazí podrobnosti o výsledcích.
```
        public static void WebSearchWithResponseFilter(WebSearchAPI client)
        {
            //var client = new WebSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                IList<string> responseFilterstrings = new List<string>() { "news" };
                var webData = client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings).Result;
                Console.WriteLine("\r\nSearched for Query# \" Microsoft \" with response filters \"news\"");

                //News
                if (webData?.News?.Value?.Count > 0)
                {
                    // find the first news result
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
## <a name="query-parameters---count-promotion-safe-search"></a>Dotazování parametry - count, povýšení, bezpečného hledání

Tento dotaz "Marie koloušku" vyhledávání pomocí `answerCount` a `promote` parametry potom zobrazí podrobnosti o výsledcích.

```
        public static void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchAPI client)
        {
            //var client = new WebSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
                var webData = client.Web.SearchAsync(query: "Lady Gaga", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict).Result;
                Console.WriteLine("\r\nSearched for Query# \" Lady Gaga \"");

                if (webData?.Videos?.Value?.Count > 0)
                {
                    var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                    if (firstVideosResult != null)
                    {
                        Console.WriteLine("Video Results #{0}", webData.Videos.Value.Count);
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
    }
```

## <a name="next-steps"></a>Další postup

[Služby cognitive services .NET SDK ukázky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).
