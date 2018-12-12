---
title: 'Rychlý start: Hledání obrázků pomocí sady SDK Vyhledávání obrázků Bingu pro C#'
description: Pomocí tohoto rychlého startu můžete vytvořit své první vyhledávání obrázků pomocí sady SDK Vyhledávání obrázků Bingu, což je obálka pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace C# posílá dotaz pro vyhledání obrázku, parsuje odpověď JSON a zobrazuje adresu URL prvního nalezeného obrázku.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 4058527263e95a75ed3d5d15cd12972170aa28fd
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53096906"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-c"></a>Rychlý start: Hledání obrázků pomocí sady SDK Vyhledávání obrázků Bingu a C#

Pomocí tohoto rychlého startu můžete vytvořit své první vyhledávání obrázků pomocí sady SDK Vyhledávání obrázků Bingu, což je obálka pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace C# posílá dotaz pro vyhledání obrázku, parsuje odpověď JSON a zobrazuje adresu URL prvního nalezeného obrázku.

Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) s dalším zpracováním chyb a poznámkami.

## <a name="prerequisites"></a>Požadavky
* Libovolná edice sady [Visual Studio 2017](https://visualstudio.microsoft.com/vs/whatsnew/).
* [Balíček NuGet Cognitive Image Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0)

K instalaci sady SDK Vyhledávání obrázků Bingu do sady Visual Studio použijte možnost `Manage NuGet Packages` v Průzkumníku řešení sady Visual Studio.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

Nejprve v sadě Visual Studio vytvořte novou konzolovou aplikaci v jazyce C#. Pak přidejte do projektu následující balíčky.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

V metodě main vašeho projektu vytvořte proměnné pro váš platný klíč předplatného, výsledky obrázků, které má vrátit Bing, a hledaný výraz. Potom pomocí daného klíče vytvořte instanci klienta vyhledávání obrázků.

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";

//initialize the client
//NOTE: If you're using version 1.2.0 or below for the Bing Image Search SDK, 
// use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.

var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>Odeslání vyhledávacího dotazu pomocí klienta

Pomocí klienta vyhledejte text dotazu:

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Parsování a zobrazení výsledku s prvním obrázkem

Parsujte výsledky obrázků vrácené v odpovědi.
Pokud odpověď obsahuje výsledky hledání, uloží se první výsledek a vytisknou se jeho podrobnosti, třeba adresa URL miniatury a původní adresa URL, společně s celkovým počtem nalezených obrázků.  

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyhledávání obrázků Bingu – kurz jednostránkové aplikace](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Vyzkoušet online interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání přístupového klíče služeb Cognitive Services zdarma](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Ukázky .NET pro sadu Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Dokumentace Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
