---
title: 'Kurz: Oříznutí obrázku pomocí sady SDK vizuálního vyhledávání Bingu'
description: Pomocí sady SDK vizuálního vyhledávání Bingzískáte přehledy od konkrétních ares na obrázku.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 7adca44f1710431ad1095cbd0da897d4c7c7f325
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74689356"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Kurz: Oříznutí obrázku pomocí sady SDK vizuálního vyhledávání Bingu pro C #

Sada SDK vizuálního vyhledávání Bingu umožňuje oříznout obrázek před vyhledáním podobných online obrázků. Tato aplikace ořízne jednu osobu z obrázku obsahujícího několik osob a poté vrátí výsledky hledání obsahující podobné obrázky nalezené online.

Úplný zdrojový kód pro tuto aplikaci je k dispozici s dalším zpracováním chyb a anotacemi na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Tento kurz ukazuje, jak:

> [!div class="checklist"]
> * Odeslání požadavku pomocí sady SDK vizuálního vyhledávání Bingu
> * Oříznutí oblasti obrázku k prohledání pomocí vizuálního vyhledávání Bingu
> * Příjem a zpracování odpovědi
> * V odpovědi najdete adresy URL položek akcí

## <a name="prerequisites"></a>Požadavky

* Libovolné vydání [Visual Studia 2019](https://www.visualstudio.com/downloads/).
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).
* Nainstalovaný balíček [NuGet pro vlastní vyhledávání](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0).
    - V Průzkumníku řešení v sadě Visual Studio klikněte pravým tlačítkem myši na váš projekt a v nabídce vyberte **Spravovat balíčky NuGet.** Nainstalujte balíček `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Při instalaci balíčku NuGet pro vlastní vyhledávání se nainstalují také následující sestavení:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Určení oblasti oříznutí obrazu

Tato aplikace plodiny oblast tohoto obrázku vedoucího týmu společnosti Microsoft. Tato ořezová plocha je definována pomocí souřadnic vlevo nahoře a vpravo dole, reprezentované jako procento celého obrazu:  

![Tým nejvyššího vedení Microsoftu](./media/MS_SrLeaders.jpg)

Tento obraz se ořízne vytvořením objektu `ImageInfo` `ImageInfo` z oblasti `VisualSearchRequest`oříznutí a načtením objektu do . Objekt `ImageInfo` také obsahuje adresu URL obrázku:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Hledání obrázků podobných oblasti oříznutí

Proměnná `VisualSearchRequest` obsahuje informace o oblasti oříznutí obrázku a jeho adrese URL. Metoda `VisualSearchMethodAsync()` získá výsledky:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Získání dat adresy URL z`ImageModuleAction`

Výsledky vizuálního `ImageTag` vyhledávání Bingu jsou objekty. Každá značka obsahuje seznam objektů `ImageAction`. Každé `ImageAction` obsahuje `Data` pole, což je seznam hodnot, které závisí na typu akce.

Můžete vytisknout různé typy s následujícím kódem:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

Dokončená aplikace vrátí:

|ActionType  |zprostředkovatele identity  | |
|---------|---------|---------|
|Stránky včetně websearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|WebsearchURL vizuálního vyhledávání    |         |
|Adresa OBRAZOvé_uživatelské místo pro vyhledávání     |         |  
|SouvisejícíVyhledávání WebSearchURL     |         |
|Entita -> WebSearchUrl     | https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4s PkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJ szgOuM5sVLs&v=1&r=https%3a%2f%2f.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Jak je uvedeno `Entity` výše, ActionType obsahuje vyhledávací dotaz Bing, který vrací informace o rozpoznatelné osobě, místě nebo věci. Typy `TopicResults` a `ImageResults` obsahují dotazy pro související obrázky. Adresy URL v seznamu odkazují na výsledky vyhledávání Bingu.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Získání adres URL `PagesIncluding` `ActionType` pro obrázky

Získání skutečné adresy URL obrázku vyžaduje přetypování, které čte `ActionType` jako `ImageModuleAction`, obsahující element `Data` se seznamem hodnot. Každá hodnota je adresa URL obrázku. Následující přetypování `PagesIncluding` typu `ImageModuleAction` akce a přečte hodnoty:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace vizuálního vyhledávání](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Viz také
> [Co je rozhraní API pro vizuální vyhledávání Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
