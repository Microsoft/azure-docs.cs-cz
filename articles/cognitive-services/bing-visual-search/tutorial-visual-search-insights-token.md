---
title: Kurz Bing Visual vyhledávání SDK ImageInsightsToken | Microsoft Docs
description: Jak používat sadu SDK Bing Visual vyhledávání k získání adres URL určeného ImageInsightsToken bitových kopií.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 578fa90f504920030b488d2b8fa3a2d0232cccce
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753647"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Kurz: Bing ImageInsightsToken SDK Visual vyhledávání a výsledky
Sada Visual vyhledávání SDK zahrnuje možnost Image online z předchozí vyhledávání, který vrátí najít `ImageInsightsToken`.  Tento příklad načte `ImageInsightsToken` a použije token do dalších hledání.  Odešle kód `ImageInsightsToken` Bing a vrátí výsledky, které zahrnují Bing vyhledávání URL a adresy URL podobné bitových kopií najít online.

## <a name="prerequisites"></a>Požadavky
Sada Visual Studio 2017. Pokud potřeby verzi můžete stáhnout zdarma komunity odsud: https://www.visualstudio.com/vs/community/.
Klíč rozhraní API kognitivní služby je vyžadovaný k ověření volání sady SDK. Zaregistrujte si bezplatnou zkušební verzi klíč. Zkušební klíč je vhodný pro sedm dní s jedno volání za sekundu. Produkčních scénářích koupit přístupový klíč. Viz také informace o cenách.
Umožňuje spouštění aplikací jádra 1.1 .NET core SDK, rozhraní .net. Základní, Framework a prostředí Runtime můžete získat z tohoto místa: https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Závislosti aplikací
Pokud chcete nastavit konzolovou aplikaci pomocí sady SDK vyhledávání webové služby Bing, přejděte na možnost Správa balíčků NuGet v Průzkumníku řešení v sadě Visual Studio. Přidejte:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage balíčky.

Instalace balíčku NuGet Web vyhledávání SDK nainstaluje taky závislosti, včetně:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Získat ImageInsightsToken z bitové kopie hledání
Tento příklad používá `ImageInsightsToken` získat následující metodu.  Další informace o toto volání najdete v tématu [Image vyhledávání SDK jazyka C# rychlý Start](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Kód hledá 'Kanadští Rockies' výsledků na dotaz a získá ImageInsightsToken. Vytiskne první obrázek Statistika tokenu, miniatur adresu url a obsahu adresa url obrázku.  Vrátí metodu `ImageInsightsToken`pro použití v následné žádosti Visual vyhledávání.

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

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Zadejte ImageInsightsToken pro požadavek Visual hledání
Tento příklad používá Statistika token vrácený předchozím metoda. Následující kód vytvoří `ImageInfo` objektu z `ImageInsightsToken` a načte do objektu ImageInfo `VisualSearchRequest`. Zadejte `ImageInsightsToken` v `ImageInfo` pro `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Pomocí Visual funkce Hledat bitové kopie z ImageInsightsToken
`VisualSearchRequest` Obsahuje informace o bitovou kopii k vyhledání v `ImageInfo` objektu.  `VisualSearchMethodAsync` Metoda získá výsledky.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Získat adresu URL data z ImageModuleAction
Visual výsledky hledání jsou `ImageTag` objekty.  Jednotlivé značky obsahuje seznam `ImageAction` objekty.  Každý `ImageAction` obsahuje `Data` pole, které je seznam hodnot, které závisí na typu akce:

Různé typy můžete získat následujícím kódem:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Vrátí hotové aplikace:

* Typ akce: MoreSizes -> WebSearchUrl:
* Typ akce: VisualSearch -> WebSearchUrl:
* Typ akce: ImageById -> WebSearchUrl:
* Typ akce: RelatedSearches -> WebSearchUrl:
* Typ akce: DocumentLevelSuggestions -> WebSearchUrl:
* Typ akce: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx, 5823.1
* Typ akce: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx, 5831.1

Jak je uvedeno v předchozím seznamu, `TopicResults` a `ImageResults` typy obsahovat dotazy pro související bitové kopie. Adresy URL v seznamu odkaz na výsledky hledání Bing.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>Typ akce PagesIncluding adresy URL bitové kopie najít Visual vyhledáváním

Získání adresy URL skutečný obrázek vyžaduje přetypování, který čte `ActionType` jako `ImageModuleAction`, který obsahuje `Data` element s seznam hodnot.  Každá hodnota je adresa URL obrázku.  Následující položky CAST `PagesIncluding` typ akce k `ImageModuleAction` a přečte hodnoty.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Další informace o těchto typech dat najdete v tématu [Image - Visual vyhledávání](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).
## <a name="complete-code"></a>Kompletní kód

Následující kód spustí předchozích příkladech. Odešle `ImageInsightsToken` v požadavku post. Potom vytiskne Bing hledání adresy URL pro každý typ akce. Pokud typ akce `PagesIncluding`, kód získá `ImageObject` položky v `Data`.  `Data` Obsahuje seznam hodnot, které jsou adresy URL bitové kopie na webových stránkách.  Zkopírujte a vložte výsledné Visual vyhledávání adresy URL do prohlížeče a zobrazit výsledky. Zkopírujte a vložte ContentUrl položky do prohlížeče zobrazíte bitové kopie.

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
[Visual hledání odpovědi](https://review.docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview?branch=pr-en-us-44614#the-response)