---
title: 'Rychlý Start: Získání přehledů imagí pomocí sady C# SDK pro vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak nahrát Image pomocí Vizuální vyhledávání Bingu SDK a získat přehled o tomto rychlém startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: b1f5274bcae1f6e59f6dea94beee810a4613d739
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446617"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Rychlý Start: Získání přehledů imagí pomocí sady Vizuální vyhledávání Bingu SDK proC#

Pomocí tohoto rychlého startu můžete začít získávat přehledy imagí z Vizuální vyhledávání Bingu služby pomocí C# sady SDK. I když Vizuální vyhledávání Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch)

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).
* Balíček NuGet Vizuální vyhledávání. 
    - V Průzkumníku řešení v sadě Visual Studio klikněte pravým tlačítkem na projekt a v nabídce vyberte `Manage NuGet Packages`. Nainstalujte balíček `Microsoft.Azure.CognitiveServices.Search.VisualSearch`. Instalace balíčků NuGet nainstaluje také následující:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V aplikaci Visual Studio vytvořte nový projekt. Pak přidejte následující direktivy.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Vytvořte instanci klienta s klíčem předplatného.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Odeslat žádost o hledání 

1. Vytvořte `FileStream` k vašim obrázkům (v tomto případě `TestImages/image.jpg`). Pak použijte klienta k odeslání žádosti o vyhledávání pomocí `client.Images.VisualSearchMethodAsync()`. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
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
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s jednou stránkou](tutorial-bing-visual-search-single-page-app.md)
