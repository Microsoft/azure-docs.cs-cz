---
title: Najít podobné obrázky z předchozí vyhledávání pomocí ImageInsightsToken – vizuální vyhledávání Bingu
titlesuffix: Azure Cognitive Services
description: Pomocí sady SDK vizuální vyhledávání Bingu k získání adres URL obrázků určené ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 609e164209bb2c920a36f293cee146cfece15fb5
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742605"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Podobně jako Image z předchozího vyhledávání pomocí ImageInsightsToken najdete

Visual SDK vyhledávání umožňuje najít Image online z předchozích hledání, které vracejí `ImageInsightsToken`.  Tato aplikace získá `ImageInsightsToken` a použije token v následném vyhledávání. Potom pošle `ImageInsightsToken` do Bingu a vrátí výsledky, které zahrnují adresy URL vyhledávání Bingu a adresy URL podobných obrázků nalezena online.

Úplný zdrojový kód pro tuto ukázku můžete najít další chyba zpracování a poznámky na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInisghtsTokens.cs).

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](http://www.mono-project.com/).
* Balíčky NuGet pro vizuální vyhledávání a pro vyhledávání obrázků. 
    - V Průzkumníku řešení v sadě Visual Studio klikněte pravým tlačítkem na projekt a v nabídce vyberte `Manage NuGet Packages`. Nainstalujte `Microsoft.Azure.CognitiveServices.Search.CustomSearch` balíčku a `Microsoft.Azure.CognitiveServices.Search.ImageSearch` balíčku. Instalace balíčků NuGet nainstalovat také následující:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Získejte ImageInsightsToken z vyhledávání obrázků Bingu SDK

Tato aplikace používá `ImageInsightsToken` získaných [SDK vyhledávání obrázků Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). V novém C# konzolovou aplikaci, vytvořte klienta volání rozhraní API pomocí `ImageSearchAPI()`. Pak pomocí `SearchAsync()` s dotazem.

```csharp
var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

První hledání způsobit pomocí Store `imageResults.Value.First()`a poté uložit obrázek insight `ImageInsightsToken`. 

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

To `ImageInsightsToken` se odešlou do vizuálního vyhledávání Bingu v požadavku.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Přidat ImageInsightsToken na požadavek pro vizuální vyhledávání

Zadejte `ImageInsightsToken` pro požadavek pro vizuální vyhledávání tak, že vytvoříte `ImageInfo` objektu z `ImageInsightsToken` obsažených v odpovědi pro vizuální vyhledávání Bingu. 

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Vyhledání imagí ze ImageInsightsToken pomocí vizuálního vyhledávání Bingu

`VisualSearchRequest` Objekt obsahuje informace o obrázku v `ImageInfo` chcete prohledat. Metoda `VisualSearchMethodAsync()` získá výsledky. Není nutné poskytovat binární soubor bitové kopie jako na obrázku je reprezentována token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterovat přes výsledky pro vizuální vyhledávání

Výsledky Vizuálního vyhledávání jsou objekty `ImageTag`.  Každá značka obsahuje seznam objektů `ImageAction`.  Každý `ImageAction` obsahuje `Data` pole, které je seznam hodnot, které závisí na typu akce. Můžete iterovat `ImageTag` objekty v `visualSearchResults.Tags`pro instanci a get `ImageAction` značky v něm. Následující ukázka zobrazí podrobnosti o `PagesIncluding` akce.

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

Získání adresy URL vlastního obrázku z akce typy vyžaduje přetypování, který čte `ActionType` jako `ImageModuleAction`, obsahující `Data` elementem seznamu hodnot.  Každá hodnota je adresa URL obrázku.  Následující kód přetypuje typ akce `PagesIncluding` na `ImageModuleAction` a přečte hodnoty.

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

Je aplikace dokončena a vrátí následující adresy URL:

|Typ akce  |zprostředkovatele identity  | |
|---------|---------|---------|
|MoreSizes WebSearchUrl ->     |         |         
|VisualSearch -> WebSearchUrl     |         |         
|ImageById -> WebSearchUrl    |         |         
|RelatedSearches -> WebSearchUrl:    |         |         
|DocumentLevelSuggestions -> WebSearchUrl:     |         |         
|TopicResults -> WebSearchUrl    | https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx, 5823.1       |         
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx, 5831.1       |         

Jak je uvedeno výše, `TopicResults` a `ImageResults` obsahovat typy dotazů pro související bitové kopie. Výsledky hledání na adresy URL odkaz ke službě Bing.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové jednostránkové aplikace](tutorial-bing-visual-search-single-page-app.md)