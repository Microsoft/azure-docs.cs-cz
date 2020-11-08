---
title: 'Kurz: oříznutí obrázku pomocí Vizuální vyhledávání Bingu SDK'
description: Pomocí sady Vizuální vyhledávání Bingu SDK získáte přehledy z konkrétního arech na obrázku.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 14b32e4d666a81b8cba0e23c99b5878b1d9d4351
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368062"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Kurz: oříznutí obrázku v sadě Vizuální vyhledávání Bingu SDK pro jazyk C #

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Sada Vizuální vyhledávání Bingu SDK umožňuje oříznout obrázek před hledáním podobných online obrázků. Tato aplikace ořízne jednu osobu z image obsahující několik lidí a potom vrátí výsledky hledání obsahující podobné obrázky, které byly nalezeny online.

Úplný zdrojový kód pro tuto aplikaci je k dispozici pro další zpracování chyb a poznámky na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Tento kurz ukazuje, jak:

> [!div class="checklist"]
> * Odeslání požadavku pomocí Vizuální vyhledávání Bingu SDK
> * Oříznutí oblasti obrázku pro hledání ve Vizuální vyhledávání Bingu
> * Přijmout a zpracovat odpověď
> * Najde adresy URL položek akcí v odpovědi.

## <a name="prerequisites"></a>Požadavky

* Všechny edice sady [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).
* Nainstalovaný balíček [NuGet pro vlastní vyhledávání](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0).
    - Z Průzkumník řešení v aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt a v nabídce vyberte možnost **Spravovat balíčky NuGet** . Nainstalujte balíček `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Při instalaci balíčku NuGet pro vlastní vyhledávání se nainstalují také následující sestavení:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Určení oblasti oříznutí obrázku

Tato aplikace ořízne oblast tohoto obrázku týmu společnosti Microsoft, který má vedoucí pracovník. Tato oblast oříznutí je definována pomocí souřadnic levého horního a dolního pravého, reprezentovaného jako procento celého obrázku:  

![Tým nejvyššího vedení Microsoftu](./media/MS_SrLeaders.jpg)

Tento obrázek je oříznut vytvořením `ImageInfo` objektu z oblasti oříznutí a načtením `ImageInfo` objektu do prvku `VisualSearchRequest` . `ImageInfo`Objekt také obsahuje adresu URL obrázku:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Vyhledat obrázky podobné oblasti oříznutí

Proměnná `VisualSearchRequest` obsahuje informace o oblasti oříznutí obrázku a jeho adrese URL. `VisualSearchMethodAsync()`Metoda získá výsledky:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Získat data adresy URL z `ImageModuleAction`

Výsledky Vizuální vyhledávání Bingu jsou `ImageTag` objekty. Každá značka obsahuje seznam objektů `ImageAction`. Každý `ImageAction` obsahuje `Data` pole, což je seznam hodnot, které závisí na typu akce.

Různé typy lze vytisknout pomocí následujícího kódu:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

Dokončená aplikace vrátí:

|ActionType  |URL  |
|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entita – > WebSearchUrl     | https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID = 03DCF882D7386A442137F49BD6596BEF&RD = 1&h = BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fsearch% 3fq% 3dSatya% 2bNadella&p = DevEx, 5380.1        |
|TopicResults-> WebSearchUrl    |  https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID = 03DCF882D7386A442137F49BD6596BEF&RD = 1&h = 3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fdiscover% 2fnadella% 2bsatya&p = DevEx, 5382.1        |
|ImageResults-> WebSearchUrl    |  https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID = 03DCF882D7386A442137F49BD6596BEF&RD = 1&h = l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fimages% 2fsearch% 3fq% 3dSatya% 2bNadella&p = DevEx, 5384.1        |

Jak je uvedeno výše, `Entity` typ akce obsahuje vyhledávací dotaz Bingu, který vrací informace o rozpoznatelné osobě, místě nebo věci. Typy `TopicResults` a `ImageResults` obsahují dotazy pro související obrázky. Adresy URL v seznamu odkazují na výsledky vyhledávání Bingu.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Získání adres URL pro `PagesIncluding` `ActionType` obrázky

Získání skutečné adresy URL obrázku vyžaduje přetypování, které čte `ActionType` jako `ImageModuleAction`, obsahující element `Data` se seznamem hodnot. Každá hodnota je adresa URL obrázku. Následující přetypování `PagesIncluding` typu akce na `ImageModuleAction` a přečte hodnoty:

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
> [Vytvoření webové aplikace Vizuální vyhledávání jednostránkového stránkování](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Viz také
> [Co je rozhraní API pro vizuální vyhledávání Bingu?](./overview.md)