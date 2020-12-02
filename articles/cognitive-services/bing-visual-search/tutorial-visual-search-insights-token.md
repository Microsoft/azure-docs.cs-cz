---
title: Hledání podobných obrázků z předchozích hledání pomocí tokenů pro Image Insights a rozhraní API pro vizuální vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Pomocí klientské knihovny Vizuální vyhledávání Bingu získat adresy URL imagí z předchozích hledání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a9005082148803423ac20b4f18dd974bb5b43b80
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486857"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Kurz: Vyhledání podobných imagí z předchozích hledání pomocí tokenu pro Image Insights

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Klientská knihovna Vizuální vyhledávání umožňuje najít image online z předchozích hledání, která vrací `ImageInsightsToken` . Tato aplikace získá `ImageInsightsToken` a použije token v následném hledání. Pak pošle `ImageInsightsToken` službě Bing a vrátí výsledky, které zahrnují vyhledávání Bingu adresy URL a adresy URL podobných imagí, které byly online.

Úplný zdrojový kód pro tento kurz najdete v části Další zpracování chyb a poznámky na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Předpoklady

* Všechny edice sady [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Pokud používáte Linux/MacOS, můžete tuto aplikaci spustit pomocí [mono](https://www.mono-project.com/).
* Balíčky NuGet Vizuální vyhledávání a Vyhledávání obrázků.
    - Z Průzkumník řešení v aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt a v nabídce vyberte možnost **Spravovat balíčky NuGet** . Nainstalujte `Microsoft.Azure.CognitiveServices.Search.CustomSearch` balíček a `Microsoft.Azure.CognitiveServices.Search.ImageSearch` balíček. Instalace balíčků NuGet nainstaluje také následující:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>Získat ImageInsightsToken z klientské knihovny Vyhledávání obrázků Bingu

Tato aplikace využívá `ImageInsightsToken` získanou prostřednictvím [klientské knihovny vyhledávání obrázků Bingu](../bing-image-search/quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp). V nové konzolové aplikaci v jazyce C# vytvořte klienta, který bude volat rozhraní API pomocí `ImageSearchClient()` . Pak použijte `SearchAsync()` s dotazem:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Uložte si první výsledky hledání pomocí `imageResults.Value.First()` a pak uložte pohled na obrázek `ImageInsightsToken` .

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

`ImageInsightsToken`Pošle se vizuální vyhledávání Bingu v žádosti.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Přidání ImageInsightsToken do žádosti o Vizuální vyhledávání

Zadejte `ImageInsightsToken` pro požadavek vizuální vyhledávání vytvořením `ImageInfo` objektu z `ImageInsightsToken` obsažených v odpovědích z vizuální vyhledávání Bingu.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Hledání imagí z ImageInsightsToken pomocí Vizuální vyhledávání Bingu

`VisualSearchRequest`Objekt obsahuje informace o obrázku ve `ImageInfo` službě, který má být prohledán. Metoda `VisualSearchMethodAsync()` získá výsledky. Není nutné zadávat binární soubor obrázku, protože je obrázek reprezentován tokenem.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterace prostřednictvím výsledků Vizuální vyhledávání

Výsledky Vizuálního vyhledávání jsou objekty `ImageTag`. Každá značka obsahuje seznam objektů `ImageAction`. Každý `ImageAction` obsahuje `Data` pole, což je seznam hodnot, které závisí na typu akce. Můžete iterovat `ImageTag` objekty v `visualSearchResults.Tags` , instance a získat `ImageAction` značku v ní. Následující ukázka vytiskne podrobnosti o `PagesIncluding` akcích:

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

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

Získání skutečných adres URL obrázků z typů akcí vyžaduje přetypování, které čte `ActionType` jako `ImageModuleAction` , který obsahuje `Data` prvek se seznamem hodnot. Každá hodnota je adresa URL obrázku.  Následující přetypování `PagesIncluding` typu akce na `ImageModuleAction` a přečte hodnoty:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Další informace o těchto typech dat najdete v článku [Obrázky – Vizuální vyhledávání](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>Vrácené adresy URL

Úplná aplikace vrátí následující adresy URL:

|ActionType  |URL  |
|---------|---------|
|MoreSizes-> WebSearchUrl     |         |
|VisualSearch-> WebSearchUrl     |         |
|ImageById-> WebSearchUrl    |         |
|RelatedSearches-> WebSearchUrl:    |         |
|DocumentLevelSuggestions-> WebSearchUrl:     |         |
|TopicResults-> WebSearchUrl    | https: \/ /www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID = 1BA795A21EAF6A63175699B71FC36B7C&RD = 1&h = BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fdiscover% 2fcanadian% 2brocky&p = DevEx, 5823.1       |
|ImageResults-> WebSearchUrl    |  https: \/ /www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID = 1BA795A21EAF6A63175699B71FC36B7C&RD = 1&h = PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fimages% 2fsearch% 3fq% 3doutdoor&p = DevEx, 5831.1       |

Jak je uvedeno výše, `TopicResults` `ImageResults` obsahují typy a dotazy pro související obrázky. Adresy URL odkazují na výsledky vyhledávání Bingu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace Vizuální vyhledávání jednostránkového stránkování](tutorial-bing-visual-search-single-page-app.md)