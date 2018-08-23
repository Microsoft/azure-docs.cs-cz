---
title: Kurz Bingu Visual Search SDK ImageInsightsToken | Dokumentace Microsoftu
description: Jak používat sadu SDK Visual pro vyhledávání Bingu k získání adres URL obrázků určené ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 8f6e7f7e88ae78fe7e8a9be4adefd689dd26d0f9
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "41988765"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Kurz: Bing Visual Search SDK ImageInsightsToken a výsledky
Vizuální vyhledávání SDK zahrnuje možnost Najít Image online předešlých hledání, který vrátí `ImageInsightsToken`.  Tento příklad načte `ImageInsightsToken` a použije token v následném vyhledávání.  Kód odešle `ImageInsightsToken` do Bingu a vrátí výsledky, které zahrnují adresy URL vyhledávání Bingu a adresy URL podobných obrázků nalezena online.

## <a name="prerequisites"></a>Požadavky
Sada Visual Studio 2017. Pokud nezbytné, můžete stáhnout zdarma komunitní verze odsud: https://www.visualstudio.com/vs/community/.
Klíč rozhraní API služeb cognitive services se vyžaduje k ověření volání sady SDK. Zaregistrovat se pro bezplatnou zkušební verzi klíče. Zkušební verze klíče je po dobu sedmi dní pomocí jednoho volání za sekundu. Pro produkční scénáře koupit přístupový klíč. Viz také informace o cenách.
Možnost spouštění rozhraní .NET core SDK, aplikace .net core 1.1. Odsud můžete získat základní rozhraní Framework a modulu Runtime: https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Závislosti aplikace
Nastavit aplikaci konzoly pomocí sady SDK webové vyhledávání Bingu, přejděte na možnost spravovat balíčky NuGet v Průzkumníku řešení v sadě Visual Studio. Přidáte:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage balíčky.

Instaluje se balíček NuGet sady SDK hledání webové nainstaluje také závislosti, včetně:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Získejte ImageInsightsToken z vyhledávání obrázků
Tento příklad používá `ImageInsightsToken` získala následující metodu.  Další informace o tomto volání najdete v tématu [Image hledání sady SDK jazyka C# rychlý Start](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Kód pro výsledky dotazu na vyhledá "Kanadské Rockies" a získá ImageInsightsToken. Vytiskne první image insights token, miniatur adresy url a obsahu adresa url obrázku.  Metoda vrátí `ImageInsightsToken`pro použití v následné žádosti pro vizuální vyhledávání.

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

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Zadejte ImageInsightsToken pro vizuální vyhledávání požadavek
Tento příklad používá insights token vrácený předchozím metodou. Následující kód vytvoří `ImageInfo` objektu z `ImageInsightsToken` a načte do objektu ImageInfo `VisualSearchRequest`. Zadejte `ImageInsightsToken` v `ImageInfo` pro `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Vyhledání imagí ze ImageInsightsToken pomocí vizuálního vyhledávání
`VisualSearchRequest` Obsahuje informace o vyhledávání v bitové kopii `ImageInfo` objektu.  `VisualSearchMethodAsync` Metoda získá výsledky.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Získání adresy URL dat z ImageModuleAction
Vizuální výsledky hledání jsou `ImageTag` objekty.  Každá značka obsahuje seznam `ImageAction` objekty.  Každý `ImageAction` obsahuje `Data` pole, které je seznam hodnot, které závisí na typu akce:

Různé typy můžete získat následujícím kódem:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Je aplikace dokončena a vrátí:

* Typ akce: MoreSizes -> WebSearchUrl:
* Typ akce: VisualSearch WebSearchUrl ->:
* Typ akce: ImageById WebSearchUrl ->:
* Typ akce: RelatedSearches WebSearchUrl ->:
* Typ akce: DocumentLevelSuggestions WebSearchUrl ->:
* Typ akce: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx, 5823.1
* Typ akce: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx, 5831.1

Jak je znázorněno v předchozím seznamu, `TopicResults` a `ImageResults` obsahovat typy dotazů pro související bitové kopie. Adresy URL v seznamu odkaz na výsledky vyhledávání Bingu.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>Adresy URL ActionType PagesIncluding zjištěných aplikací pro vizuální vyhledávání obrázků

Získání adresy URL skutečný obraz vyžaduje přetypování, který čte `ActionType` jako `ImageModuleAction`, obsahující `Data` elementem seznamu hodnot.  Každá hodnota je adresa URL obrázku.  Následující přetypování `PagesIncluding` typ akce `ImageModuleAction` a načte hodnoty.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Další informace o těchto typech dat najdete v části [Image - vizuálního vyhledávání](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).
## <a name="complete-code"></a>Kompletní kód

Následující kód běží předchozí příklady. Odešle `ImageInsightsToken` v požadavku post. Potom zobrazí Bing hledání adresy URL pro každý typ akce. Pokud je typ akce `PagesIncluding`, získá kód `ImageObject` položky v `Data`.  `Data` Obsahuje seznam hodnot, které jsou adresy URL obrázků na webových stránkách.  Zkopírujte a vložte výsledný Visual Search adresy URL do prohlížeče a zobrazit výsledky. Zkopírovat a vložit položky ContentUrl do prohlížeče a zobrazit obrázky.

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
## <a name="next-steps"></a>Další postup
[Vizuální hledání odpovědí](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
