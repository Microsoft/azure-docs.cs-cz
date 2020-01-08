---
title: 'Rychlý Start: provedení hledání zpráv pomocí sady SDK pro C# vyhledávání zpráv Bingu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete vyhledat zprávy pomocí sady Vyhledávání zpráv Bingu SDK C#pro a zpracovat odpověď.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: fe1dbd132f1cbacac59b0a9055b698865c2f7090
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75383171"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-c"></a>Rychlý Start: provedení zprávy s Vyhledávání zpráv Bingu SDK proC#

V tomto rychlém startu můžete začít hledat zprávy s Vyhledávání zpráv Bingu SDK pro C#. I když Vyhledávání zpráv Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch)

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/).
* Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet.
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).

* [Balíček NuGet sady vyhledávání zpráv Bingu SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0). Instalace tohoto balíčku také nainstaluje následující:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Pokud chcete nastavit konzolovou aplikaci pomocí sady SDK Bingu pro vyhledávání zpráv, přejděte v sadě Visual Studio v Průzkumníku řešení na možnost `Manage NuGet Packages`.  Přidejte balíček `Microsoft.Azure.CognitiveServices.Search.NewsSearch`.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nové C# řešení konzoly v aplikaci Visual Studio. Pak přidejte následující do hlavního souboru kódu.
    
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
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
