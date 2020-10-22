---
title: Rychlý Start klientské knihovny Vyhledávání obrázků Bingu C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: 122e44da7bbf4229f932eefdae4c70dc49f43bfe
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371272"
---
Pomocí tohoto rychlého startu můžete vytvořit první hledání obrázků pomocí klientské knihovny Vyhledávání obrázků Bingu. 

Knihovna vyhledávání klientů je obálkou pro REST API a obsahuje stejné funkce. 

Vytvoříte aplikaci jazyka C#, která odešle dotaz na hledání obrázků, analyzuje odpověď JSON a zobrazí adresu URL prvního vráceného obrázku.

Zdrojový kód pro tuto ukázku je k dispozici [na GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) s dalšími zpracováním chyb a poznámkami.

## <a name="prerequisites"></a>Předpoklady

* Pokud používáte systém Windows, libovolné vydání sady [Visual Studio 2017 nebo novější](https://visualstudio.microsoft.com/vs/whatsnew/)
* Pokud používáte macOS nebo Linux, [vs Code](https://code.visualstudio.com) s [nainstalovaným rozhraním .NET Core](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
* [Bezplatné předplatné Azure](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

Viz také [Cognitive Services vyhledávání BINGU API pro ceny](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-a-console-project"></a>Vytvoření projektu konzoly

Nejprve vytvořte novou konzolovou aplikaci v jazyce C#.

## <a name="create-a-console-project"></a>Vytvoření projektu konzoly

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. Vytvořte nové řešení konzoly s názvem *BingImageSearch* v aplikaci Visual Studio.
    
1. Přidání [balíčku pro rozpoznávání vyhledávání obrázků NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch)
    1. V **Průzkumník řešení**klikněte pravým tlačítkem na svůj projekt.
    1. Vyberte **Spravovat balíčky NuGet**.
    1. Vyhledejte a vyberte *Microsoft. Azure. cognitiveservices Account. Search. ImageSearch*a pak balíček nainstalujte.
    
# <a name="vs-code"></a>[VS Code](#tab/vscode)

1. Otevřete okno terminálu v VS Code.
1. Vytvořte nový projekt konzoly s názvem *BingImageSearch* zadáním následujícího kódu v okně terminálu:
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. Otevřete složku *BingImageSearch* v vs Code.
1. Přidáním následujícího kódu do okna terminálu přidejte NuGetPackage [balíčku NuGet pro rozpoznávání vyhledávání obrázků](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) :

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>Inicializace aplikace


1. Nahraďte všechny `using` příkazy v *program.cs* následujícím kódem:

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. V `Main` metodě vašeho projektu vytvořte proměnné pro svůj platný klíč předplatného, výsledky obrázků, které mají být vráceny bingem, a hledaný termín. Potom pomocí daného klíče vytvořte instanci klienta vyhledávání obrázků.

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>Odeslání vyhledávacího dotazu pomocí klienta
    
Stále v `Main` metodě použijte klienta k hledání textu dotazu:
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Parsování a zobrazení výsledku s prvním obrázkem

Parsujte výsledky obrázků vrácené v odpovědi. 

Pokud odpověď obsahuje výsledky hledání, uložte první výsledek a vytiskněte některé z jeho podrobností.

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledávání obrázků Bingu – kurz jednostránkové aplikace](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Viz také

* [Co je Vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Vyzkoušet online interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ukázky .NET pro sadu Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Dokumentace k Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
