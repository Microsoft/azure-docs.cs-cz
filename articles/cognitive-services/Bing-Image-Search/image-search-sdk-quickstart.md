---
title: 'Rychlý start: Vyhledávání obrázků s využitím SDK vyhledávání obrázků Bingu a C#'
description: V tomto rychlém startu použijte k vyhledání a Image můžete vyhledat na webu pomocí SDK vyhledávání obrázků Bingu a C#.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 9e0decb29224b5ad684e1242b8f93e091e08e6b6
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579246"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-c"></a>Rychlý start: Vyhledejte imagí pomocí SDK vyhledávání obrázků Bingu a C#

V tomto rychlém startu používejte k vytvoření vaší první vyhledávání obrázků pomocí SDK vyhledávání Bingu Image, který tvoří obálku pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace C# odešle dotaz vyhledávání obrázků, analyzuje odpověď JSON a zobrazí adresa URL první obrázku vrátil.

Zdrojový kód pro tuto ukázku je k dispozici [na Githubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) zpracování dalších chyb a poznámky.

## <a name="prerequisites"></a>Požadavky

* Libovolná edice [Visual Studio 2017](https://visualstudio.microsoft.com/vs/whatsnew/).
* [Balíček NuGet hledání Cognitive Image](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0).

K instalaci sady SDK vyhledávání obrázků Bingu v sadě visual studio můžete použít `Manage NuGet Packages` možnost z Průzkumníku řešení v sadě Visual Studio.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytváření a inicializace aplikace

Nejprve vytvořte novou aplikaci konzoly C# v sadě Visual Studio. Pak přidejte následující balíčky do vašeho projektu.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

V hlavní metodě váš projekt Vytvořte proměnné pro klíč předplatného. Platné výsledky hledání obrázků, který se má vrátit v Bingu a hledaný termín. Potom vytvořte instanci klienta vyhledávání obrázků pomocí klíče.

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";
//initialize the client
var client = new ImageSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>Odeslat dotaz pomocí klienta

Pomocí klienta na hledání text dotazu:

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analyzovat a zobrazovat první výsledek bitové kopie

Analyzovat výsledky hledání obrázků, vrátila v odpovědi.
Pokud odpověď obsahuje výsledky hledání, uloží první výsledek a vytiskne jeho podrobnosti, jako je například miniatury adresa URL, původní adresu URL, společně s celkový počtem vrátil bitové kopie.  

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
> [Kurz jednostránkovou aplikaci pro vyhledávání obrázků Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je pro vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Zkuste online interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání bezplatné přístupového klíče služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Ukázky .NET pro Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Dokumentace ke službě Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční dokumentace rozhraní API pro vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)