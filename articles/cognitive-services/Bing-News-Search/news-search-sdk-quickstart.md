---
title: 'Rychlý start: Hledání zpráv - SDK vyhledávání zpráv BinguC#'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu slouží k vyhledání zpráv pomocí sady SDK vyhledávání zpráv Bingu pro Python a zpracování odpovědi.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/30/2018
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 5b3e68765fbcff12dcb5337aec38623b8994882c
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156795"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-c"></a>Rychlý start: Pomocí sady SDK vyhledávání zpráv Bingu pro vyhledávání zprávC#

Sada SDK Bingu pro vyhledávání zpráv obsahuje funkce rozhraní REST API pro dotazy na zprávy a parsování výsledků. 

[Zdrojový kód ukázek sady SDK Bingu pro vyhledávání zpráv v jazyce C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch) je k dispozici na GitHubu.

## <a name="application-dependencies"></a>Závislosti aplikace
Načtěte si pod **Search** (Hledání) [přístupový klíč služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).  Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).  

Pokud chcete nastavit konzolovou aplikaci pomocí sady SDK Bingu pro vyhledávání zpráv, přejděte v sadě Visual Studio v Průzkumníku řešení na možnost `Manage NuGet Packages`.  Přidejte balíček `Microsoft.Azure.CognitiveServices.Search.NewsSearch`.

Instalací [balíčku NuGet sady SDK Bingu pro vyhledávání zpráv](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0) se také nainstalují závislosti, včetně:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="news-search-client"></a>Klient pro vyhledávání zpráv
Chcete-li vytvořit instanci `NewsSearchClient`, přidávat direktiva using:
```
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

```
Potom vytvořte instanci klienta:
```
var client = new NewsSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Pomocí klienta vyhledejte text dotazu:
```
var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

```
Parsujte zprávy vrácené ve výsledcích předchozího dotazu:
```
if (newsResults.Value.Count > 0)
{
    var firstNewsResult = newsResults.Value[0];

    Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
    Console.WriteLine($"News result count: {newsResults.Value.Count}");
    Console.WriteLine($"First news name: {firstNewsResult.Name}");
    Console.WriteLine($"First news url: {firstNewsResult.Url}");
    Console.WriteLine($"First news description: {firstNewsResult.Description}");
    Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
    Console.WriteLine($"First news provider: {firstNewsResult.Provider[0].Name}");
}

else
{
    Console.WriteLine("Couldn't find news results!");
}

```
## <a name="complete-console-application"></a>Kompletní konzolová aplikace

Následující konzolová aplikace spustí dříve definovaný dotaz a vyhledá zprávy pro výraz „Quantum Computing“ (Kvantové výpočty). Požadavek obsahuje parametry `market` a `count`. Kód zkontroluje počet výsledků a pro první výsledek hledání zpráv vypíše celkový odhadovaný počet shod (`totalEstimatedMatches`), název (`name`), adresu URL (`url`), popis (`description`), datum publikování (`published time`) a název (`name`) zprostředkovatele (`provider`).

```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

namespace NewsSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new NewsSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            try
            {
                var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
                Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            // Include these methods to run queries explained under headings following.
            // NewsSearchWithFilters(client);
            // NewsCategory(client);
            // TrendingTopics(client);

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}

```
## <a name="recent-news-freshness-and-sortby-parameters"></a>Nejnovější zprávy a parametry freshness a sortBy
Následující kód vyhledá nejnovější zprávy pro výraz „Artificial Intelligence“ (Umělá inteligence) s využitím parametrů `freshness` a `sortBy`. Kód zkontroluje počet výsledků a pro první výsledek hledání zpráv vypíše celkový odhadovaný počet shod (`totalEstimatedMatches`), název (`name`), adresu URL (`url`), popis (`description`), datum publikování (`published time`) a název (`name`) zprostředkovatele.
```
        public static void NewsSearchWithFilters(NewsSearchClient client)
        {
            try
            {
                var newsResults = client.News.SearchAsync(query: "Artificial Intelligence", market: "en-us", freshness: "Week", sortBy: "Date").Result;
                Console.WriteLine("Search most recent news for query \"Artificial Intelligence\" with freshness and sortBy");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nTotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="category-news-safe-search"></a>Zprávy z kategorie a bezpečné hledání
Následující kód vyhledá zprávy z kategorie filmů a televizní zábavy s využitím bezpečného hledání.  Kód zkontroluje počet výsledků a pro první výsledek hledání zpráv vypíše kategorii (`category`), název (`name`), adresu URL (`url`), popis (`description`), datum publikování (`published time`) a název (`name`) zprostředkovatele.
```
        public static void NewsCategory(NewsSearchClient client)
        {
            try
            {
                var newsResults = client.News.CategoryAsync(category: "Entertainment_MovieAndTV", market: "en-us", safeSearch: "strict").Result;
                Console.WriteLine("Search category news for movie and TV entertainment with safe search");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nNews result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news category: {firstNewsResult.Category}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="trending-topics"></a>Populární témata
Následující kód vyhledá populární témata zpráv v Bingu. Kód zkontroluje počet výsledků a pro první výsledek hledání zpráv vypíše název (`name`), text dotazu (`text of query`), adresu URL webu (`webSearchUrl`), adresu URL zprávy (`newsSearchUrl`) a adresu URL obrázku(`image.Url`).
```
        public static void TrendingTopics(NewsSearchClient client)
        {
            try
            {
                var trendingTopics = client.News.TrendingAsync(market: "en-us").Result;
                Console.WriteLine("Search news trending topics in Bing");

                if (trendingTopics == null)
                {
                    Console.WriteLine("Didn't see any news trending topics..");
                }
                else
                {
                    if (trendingTopics.Value.Count > 0)
                    {
                        var firstTopic = trendingTopics.Value.First();

                        Console.WriteLine($"\r\nTrending topics count: {trendingTopics.Value.Count}");
                        Console.WriteLine($"First topic name: {firstTopic.Name}");
                        Console.WriteLine($"First topic query: {firstTopic.Query.Text}");
                        Console.WriteLine($"First topic image url: {firstTopic.Image.Url}");
                        Console.WriteLine($"First topic webSearchUrl: {firstTopic.WebSearchUrl}");
                        Console.WriteLine($"First topic newsSearchUrl: {firstTopic.NewsSearchUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news trending topics!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="next-steps"></a>Další postup

[Ukázky kognitivních služeb sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
