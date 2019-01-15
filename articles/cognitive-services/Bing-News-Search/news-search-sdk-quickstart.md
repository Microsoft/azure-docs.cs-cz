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
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 66f743273f9937902c3d39a0fc4dd2f034ab8d10
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260240"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-c"></a>Rychlý start: Pomocí sady SDK vyhledávání zpráv Bingu pro vyhledávání zprávC#

Pomocí tohoto rychlého startu zahájíte zpráv pomocí sady SDK vyhledávání zpráv Bingu pro vyhledávání C#. Při vyhledávání zpráv Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch).

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet.
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](http://www.mono-project.com/).

* [Balíček NuGet sady SDK pro vyhledávání zpráv Bingu](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0). Instaluje se tento balíček nainstaluje taky následující:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Pokud chcete nastavit konzolovou aplikaci pomocí sady SDK Bingu pro vyhledávání zpráv, přejděte v sadě Visual Studio v Průzkumníku řešení na možnost `Manage NuGet Packages`.  Přidejte balíček `Microsoft.Azure.CognitiveServices.Search.NewsSearch`.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nový C# konzole řešení v sadě Visual Studio. Pak přidejte následující kód do souboru hlavní kód.
    
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    ```

2. Vytvořte proměnnou pro vaše rozhraní API key hledaný termín a potom vytvořte instanci klienta hledání zpráv s ním.

    ```csharp
    var key = "YOUR-ACCESS-KEY";
    var searchTerm = "Quantum Computing";
    var client = new NewsSearchClient(new ApiKeyServiceClientCredentials(key));
    ```

## <a name="send-a-request-and-parse-the-result"></a>Odešle žádost a analyzovat výsledky

1. Odeslat žádost o vyhledávání službě Bingu pro vyhledávání zpráv pomocí klienta:
    ```csharp
    var newsResults = client.News.SearchAsync(query: searchTerm, market: "en-us", count: 10).Result;
    ```

2. Pokud nebyly vráceny žádné výsledky, analyzovat je:

    ```csharp
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
    Console.WriteLine("Enter any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
[Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)