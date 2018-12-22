---
title: 'Rychlý start: Získejte přehledy obrázků s využitím SDK vizuální vyhledávání Bingu proC#'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak nahrát image pomocí sady SDK vizuální vyhledávání Bingu a získávat přehledy o něm.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 05/16/2018
ms.author: v-gedod
ms.openlocfilehash: c589cc14fd429385dd47aad489e827804916d406
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53741213"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Rychlý start: Získejte přehledy obrázků s využitím SDK vizuální vyhledávání Bingu proC#

Můžete začít získat přehledy obrázků ze služby pro vizuální vyhledávání Bingu, v tomto rychlém startu pomocí C# SDK. Zatímco kompatibilní s Většina programovacích jazyků rozhraní REST API pro vizuální vyhledávání Bingu, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](http://www.mono-project.com/).
* Balíček NuGet pro vizuální vyhledávání. 
    - V Průzkumníku řešení v sadě Visual Studio klikněte pravým tlačítkem na projekt a v nabídce vyberte `Manage NuGet Packages`. Nainstalujte balíček `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Instalace balíčků NuGet nainstalovat také následující:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V sadě Visual Studio vytvořte nový projekt. Pak přidejte následující direktivy.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Vytvoření instance klienta s klíči předplatného.
    
    ```csharp
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Odeslat žádost o vyhledávání 

1. Vytvoření `FileStream` do vašich imagí (v tomto případě `TestImages/image.jpg`). Potom pomocí klienta odeslat žádost o vyhledávání pomocí `client.Images.VisualSearchMethodAsync()`. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open;
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. Parsujte výsledky předchozího dotazu:

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags.First();
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions.First();
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové jednostránkové aplikace](tutorial-bing-visual-search-single-page-app.md)
