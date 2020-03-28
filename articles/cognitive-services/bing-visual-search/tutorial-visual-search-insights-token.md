---
title: Najít podobné obrázky z předchozích vyhledávání pomocí ImageInsightsToken - Vizuální vyhledávání Bing
titleSuffix: Azure Cognitive Services
description: Pomocí sady SDK vizuálního vyhledávání Bingu můžete získat adresy URL obrázků určených tokenem ImageInsightsToken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.openlocfilehash: d005800ed317ff21389f18e4440858ea11042e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370078"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Kurz: Vyhledání podobných obrázků z předchozích vyhledávání pomocí tokenu přehledů obrázků

Sada Visual Search SDK umožňuje vyhledat obrázky online `ImageInsightsToken`z předchozích hledání, která vracejí soubor . Tato aplikace `ImageInsightsToken` získá a používá token v následném hledání. Potom odešle `ImageInsightsToken` bing a vrátí výsledky, které zahrnují adresy URL vyhledávání Bing a adresy URL podobných obrázků nalezených online.

Úplný zdrojový kód pro tento kurz lze nalézt s další zpracování chyb a poznámky na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Požadavky

* Libovolné vydání [Visual Studia 2019](https://www.visualstudio.com/downloads/).
* Pokud používáte Linux/MacOS, můžete spustit tuto aplikaci pomocí [Mono](https://www.mono-project.com/).
* NuGet Visual Search a image Search balíčky.
    - V Průzkumníku řešení v sadě Visual Studio klikněte pravým tlačítkem myši na váš projekt a v nabídce vyberte **Spravovat balíčky NuGet.** Nainstalujte `Microsoft.Azure.CognitiveServices.Search.CustomSearch` balíček `Microsoft.Azure.CognitiveServices.Search.ImageSearch` a balíček. Instalace balíčků NuGet také nainstaluje následující:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Získání imageInsightsToken z bing image search SDK

Tato aplikace `ImageInsightsToken` používá získané prostřednictvím [Bing Image Search SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). V nové aplikaci konzoly C# vytvořte `ImageSearchClient()`klienta pro volání rozhraní API pomocí . Potom `SearchAsync()` použijte s dotazem:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Uložte první výsledek `imageResults.Value.First()`hledání pomocí aplikace a `ImageInsightsToken`potom uložte přehled obrázků .

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Tato `ImageInsightsToken` možnost je odeslána do vizuálního vyhledávání Bingu v žádosti.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Přidání tokenu ImageInsightsToken do požadavku vizuálního vyhledávání

Zadejte `ImageInsightsToken` požadavek vizuálního hledání `ImageInfo` vytvořením `ImageInsightsToken` objektu z odpovědí obsažených z vizuálního vyhledávání Bing.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Hledání obrázků z tokenu ImageInsightsToken pomocí vizuálního vyhledávání Bingu

Objekt `VisualSearchRequest` obsahuje informace o `ImageInfo` obrázku, který má být prohledán. Metoda `VisualSearchMethodAsync()` získá výsledky. Není třeba zadat binární obrázek, jako obrázek je reprezentován token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterate prostřednictvím výsledků vizuálního hledání

Výsledky Vizuálního vyhledávání jsou objekty `ImageTag`. Každá značka obsahuje seznam objektů `ImageAction`. Každé `ImageAction` obsahuje `Data` pole, což je seznam hodnot, které závisí na typu akce. Můžete iterate prostřednictvím `ImageTag` `visualSearchResults.Tags`objektů v , například, a získat `ImageAction` značku v něm. Následující ukázka vytiskne podrobnosti o akcích: `PagesIncluding`

```csharp
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
```

### <a name="pagesincluding-actiontypes"></a>Stránky včetně actiontypes

Získání skutečné image adresy URL z typů akcí `ActionType` vyžaduje `ImageModuleAction`přetypování, který čte as , který obsahuje `Data` prvek se seznamem hodnot. Každá hodnota je adresa URL obrázku.  Následující přetypování `PagesIncluding` typu `ImageModuleAction` akce a přečte hodnoty:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Další informace o těchto typech dat najdete v článku [Obrázky – Vizuální vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>Vrácené adresy URL

Úplná aplikace vrátí následující adresy URL:

|ActionType  |zprostředkovatele identity  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQmzdKFyYBusjLxxgO42kzq 1Geh7RucVVQvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveS DRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Jak je uvedeno `TopicResults` `ImageResults` výše, a typy obsahují dotazy na související obrázky. Adresy URL odkazují na výsledky hledání Bingu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace vizuálního vyhledávání](tutorial-bing-visual-search-single-page-app.md)
