---
title: Rychlý Start klientské knihovny Vyhledávání zpráv Bingu C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: fbc7d7b35b46e96e9cd007a3794a8ccc561f91e4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "84878899"
---
V tomto rychlém startu můžete začít vyhledávat zprávy pomocí Vyhledávání zpráv Bingu klientské knihovny pro C#. I když Vyhledávání zpráv Bingu má REST API kompatibilní s většinou programovacích jazyků, Klientská knihovna poskytuje snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch).

## <a name="prerequisites"></a>Předpoklady

* Libovolná edice sady [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/).
* Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet.
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).

* [Balíček NuGet sady vyhledávání zpráv Bingu SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/2.0.0). Instalace tohoto balíčku také nainstaluje následující:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Chcete-li nastavit konzolovou aplikaci pomocí klientské knihovny Vyhledávání zpráv Bingu, přejděte na `Manage NuGet Packages` možnost v Průzkumník řešení v sadě Visual Studio.  Přidejte balíček `Microsoft.Azure.CognitiveServices.Search.NewsSearch`.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nové řešení konzoly C# v aplikaci Visual Studio. Pak přidejte následující do hlavního souboru kódu.
    
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    ```

2. Vytvořte proměnnou pro klíč rozhraní API, hledaný termín a potom vytvořte instanci klienta vyhledávání zpráv.

    ```csharp
    var key = "YOUR-ACCESS-KEY";
    var searchTerm = "Quantum Computing";
    var client = new NewsSearchClient(new ApiKeyServiceClientCredentials(key));
    ```

## <a name="send-a-request-and-parse-the-result"></a>Odeslat žádost a analyzovat výsledek

1. Použijte klienta k odeslání žádosti o vyhledávání službě Vyhledávání zpráv Bingu:
    ```csharp
    var newsResults = client.News.SearchAsync(query: searchTerm, market: "en-us", count: 10).Result;
    ```

2. Pokud byly vráceny výsledky, analyzujte je:

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../../tutorial-bing-news-search-single-page-app.md)
