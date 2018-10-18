---
title: 'Kurz: ImageInsightsToken – Vizuální vyhledávání Bingu'
titlesuffix: Azure Cognitive Services
description: Jak používat sadu SDK Vizuálního vyhledávání Bingu k získání adres URL obrázků určených parametrem ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 06d6bc8e53276b5542210c2843d7221d6fd79c09
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386430"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Kurz: ImageInsightsToken sady SDK Vizuálního vyhledávání Bingu a výsledky
Sada SDK Vizuálního vyhledávání zahrnuje možnost najít obrázky online z předešlého hledání, která vrátí `ImageInsightsToken`.  Tento příklad načte `ImageInsightsToken` a použije tento token v následném hledání.  Kód pošle `ImageInsightsToken` do Bingu a vrátí výsledky, které zahrnují adresy URL Vyhledávání Bingu a adresy URL podobných obrázků nalezených online.

## <a name="prerequisites"></a>Požadavky
Sada Visual Studio 2017. V případě potřeby si můžete stáhnout komunitní verzi zdarma odsud: https://www.visualstudio.com/vs/community/.
Vyžaduje se klíč rozhraní API kognitivních služeb k ověření volání sady SDK. Zaregistrujte si bezplatný zkušební klíč. Zkušební verze klíče je platná sedm dní s jedním voláním za sekundu. Do výroby si kupte přístupový klíč. Podívejte se také na informace o cenách.
Možnost spouštět .NET core SDK, aplikace .net core 1.1. CORE, Framework a modul Runtime můžete získat odsud: https://www.microsoft.com/net/download/.

## <a name="application-dependencies"></a>Závislosti aplikace
Pokud chcete nastavit konzolovou aplikaci pomocí sady SDK Vyhledávání na webu Bingu, přejděte v sadě Visual Studio v Průzkumníku řešení na možnost Spravovat balíčky NuGet. Přidejte:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* balíčky Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage.

Instalací balíčku NuGet Vyhledávání na webu se také nainstalují závislosti, včetně:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Načtení ImageInsightsToken z Vyhledávání obrázků

Tento příklad používá `ImageInsightsToken` získaný následující metodou.  Další informace o tomto volání najdete v článku [Rychlý start pro sadu SDK Vyhledávání obrázků jazyka C#](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Kód vyhledá výsledky dotazu na „Canadian Rockies“ a načte ImageInsightsToken. Vytiskne token insights prvního obrázku, adresu url miniatury a adresu url obsahu obrázku.  Metoda vrátí `ImageInsightsToken` pro použití v následné žádosti Vizuálního vyhledávání.

```
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }
```

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Určení tokenu ImageInsightsToken pro žádost Vizuálního vyhledávání

Tento příklad používá token insights vrácený předchozí metodou. Následující kód vytvoří objekt `ImageInfo` z `ImageInsightsToken` a načte objekt ImageInfo do `VisualSearchRequest`. Určete `ImageInsightsToken` v `ImageInfo` pro `VisualSearchRequest`.

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Vyhledání obrázků z tokenu ImageInsightsToken pomocí Vizuálního vyhledávání

`VisualSearchRequest` obsahuje informace o obrázku, který se má vyhledat v objektu `ImageInfo`.  Metoda `VisualSearchMethodAsync` získá výsledky.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Získání dat adresy URL z ImageModuleAction
Výsledky Vizuálního vyhledávání jsou objekty `ImageTag`.  Každá značka obsahuje seznam objektů `ImageAction`.  Každé `ImageAction` obsahuje pole `Data`, které je seznamem hodnot závislých na typu akce:

Různé typy můžete získat následujícím kódem:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Dokončená aplikace vrátí:

* ActionType: MoreSizes -> WebSearchUrl:
* ActionType: VisualSearch -> WebSearchUrl:
* ActionType: ImageById -> WebSearchUrl:
* ActionType: RelatedSearches -> WebSearchUrl:
* ActionType: DocumentLevelSuggestions -> WebSearchUrl:
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1

Jak je znázorněno v předchozím seznamu, typy `TopicResults` a `ImageResults` obsahují dotazy pro související obrázky. Adresy URL v seznamu odkazují na výsledky vyhledávání Bingu.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>Adresy URL PagesIncluding ActionType obrázků nalezených Vizuálním vyhledáváním

Získání skutečné adresy URL obrázku vyžaduje přetypování, které čte `ActionType` jako `ImageModuleAction`, obsahující element `Data` se seznamem hodnot.  Každá hodnota je adresa URL obrázku.  Následující kód přetypuje typ akce `PagesIncluding` na `ImageModuleAction` a přečte hodnoty.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Další informace o těchto typech dat najdete v článku [Obrázky – Vizuální vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="complete-code"></a>Celý kód

Následující kód spustí předchozí příklady. Pošle `ImageInsightsToken` v žádosti POST. Potom vytiskne adresy URL Vyhledávání Bingu pro každý ActionType. Pokud je ActionType `PagesIncluding`, získá kód položky `ImageObject` v `Data`.  `Data` obsahuje seznam hodnot, které jsou adresami URL obrázků na webových stránkách.  Zkopírujte a vložte výsledné adresy URL Vizuálního vyhledávání do prohlížeče a zobrazte výsledky. Zkopírujte a vložte položky ContentUrl do prohlížeče a zobrazte obrázky.

```
using System;
using System.IO;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;

namespace VisualSearchFeatures
{
    class Program
    {
        static void Main(string[] args)
        {
            String subscriptionKey = "YOUR-ACCESS-KEY";

            insightsToken = ImageResults(subscriptionKey);

            VisualSearchInsightsToken(subscriptionKey, insightsToken);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        // Searches for results on query (Canadian Rockies) and gets an ImageInsightsToken.
        // Also prints first image insights token, thumbnail url, and image content url.
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }


        // This method will get Visual Search results from an imageInsightsToken obtained from the return value of the ImageResults method.
        // The method includes imageInsightsToken the in a knowledgeRequest parameter, along with a cropArea object. 
        // It prints out the imageInsightsToken uploaded in the request.
        // Finally the example prints URLs of images found using the imageInsightsToken.

        public static void VisualSearchInsightsToken(string subscriptionKey, string insightsTok)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via an insights token, in the ImageInfo object.
                ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);

                // An image binary is not necessary here, as the image is specified by insights token.
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with imageInsightsToken and knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {insightsTok}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

                    if (visualSearchResults.Tags.Count > 0)
                    {
                        // List of tags
                        foreach (ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            {
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                                    {
                                        Console.WriteLine("ContentURL: " + o.ContentUrl);
                                    }
                                }
                            }
                        }
                    }

                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }

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

## <a name="next-steps"></a>Další kroky
[Odpověď Vizuálního vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
