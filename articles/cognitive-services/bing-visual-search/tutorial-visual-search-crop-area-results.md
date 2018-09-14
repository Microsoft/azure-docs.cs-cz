---
title: Oblast oříznutí Visual SDK vyhledávání Bingu výsledky kurzu | Dokumentace Microsoftu
description: Jak používat sadu SDK Visual pro vyhledávání Bingu k získání adres URL obrázků podobný oříznout oblasti nahraný obrázek.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: dd51ed7c710cc51a9fe0e63e55aa0d2c4ea24bee
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574485"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Kurz: Oblast oříznutí obrázku Visual SDK vyhledávání Bingu a výsledky
Vizuální vyhledávání SDK zahrnuje možnost vyberte oblast obrázku a vyhledání online imagí, podobné oblasti oříznutí větší obrázek.  Tento příklad určuje oblast oříznutí zobrazení jedné osobě bitovou kopii, která obsahuje několik lidí.  Kód odešle oblast oříznutí a adresa URL obrázku, který větší a vrátí výsledky, které zahrnují adresy URL vyhledávání Bingu a adresy URL podobných obrázků nalezena online.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) Chcete-li získat tento kód běžící na Windows. (Bude stačit bezplatná verze Community Edition.)

Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pomocí rozhraní API Bingu pro vyhledávání. [Bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) stačí pro účely tohoto rychlého startu. Budete potřebovat přístupový klíč získáte při aktivaci vaší bezplatné zkušební verze, nebo můžete použít klíč placené předplatné z řídicího panelu Azure.

## <a name="application-dependencies"></a>Závislosti aplikace
Nastavit aplikaci konzoly pomocí sady SDK webové vyhledávání Bingu, přejděte na možnost spravovat balíčky NuGet v Průzkumníku řešení v sadě Visual Studio. Přidání balíčku Microsoft.Azure.CognitiveServices.Search.VisualSearch.

Instaluje se balíček NuGet sady SDK hledání webové nainstaluje také závislosti, včetně:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Oblast obrázku a oříznutí.
Následující obrázek ukazuje vedení týmů ve společnosti Microsoft.  Pomocí sady SDK vizuální hledání, nahrajte oblast oříznutí obrázku jsme najít další Image a webové stránky, které zahrnují entitu ve vybrané oblasti větší obrázek.  Entita v tomto případě je osoba.

![Tým vedení společnosti Microsoft](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Zadejte oblast oříznutí jako ImageInfo v VisualSearchRequest
Tento příklad používá oblast oříznutí předchozím obrázku, který určuje levý horní a dolní souřadnice pravého podle procenta celé image.  Následující kód vytvoří `ImageInfo` objekt z oblasti oříznutí a zatížení `ImageInfo` objektů do `VisualSearchRequest`.  `ImageInfo` Objekt zahrnuje také adresa URL obrázku online.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Hledání obrázků podobný oříznout oblasti
`VisualSearchRequest` Obsahuje oříznutí oblasti informace o bitové kopie a její adresu URL.  `VisualSearchMethodAsync` Metoda získá výsledky.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Získání adresy URL dat z ImageModuleAction
Vizuální výsledky hledání jsou `ImageTag` objekty.  Každá značka obsahuje seznam `ImageAction` objekty.  Každý `ImageAction` obsahuje `Data` pole, které je seznam hodnot, které závisí na typu akce:

Různé typy můžete získat následujícím kódem:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Je aplikace dokončena a vrátí:

* Typ akce: PagesIncluding WebSearchURL:
* Typ akce: MoreSizes WebSearchURL:
* Typ akce: VisualSearch WebSearchURL:
* Typ akce: ImageById WebSearchURL: 
* Typ akce: RelatedSearches WebSearchURL:
* Typ akce: WebSearchUrl -> Entity: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5380.1
* Typ akce: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx, 5382.1
* Typ akce: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5384.1

Jak je znázorněno v předchozím seznamu, `Entity` `ActionType` obsahuje dotaz Bingu pro vyhledávání, který vrací informace o rozpoznatelných osobě, místě nebo věc.  `TopicResults` a `ImageResults` obsahovat typy dotazů pro související bitové kopie. Adresy URL v seznamu odkaz na výsledky vyhledávání Bingu.


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

## <a name="complete-code"></a>Kompletní kód

Následující kód běží předchozí příklady. Odešle obrázek binární v textu požadavku post, spolu s objektem cropArea a vytiskne Bing hledání adresy URL pro každý typ akce. Pokud typ akce PagesIncluding, kód získá položky ImageObject ImageObject data.  Data obsahují seznam hodnot, které jsou adresy URL obrázků na webových stránkách.  Zkopírujte a vložte výsledný Visual Search adresy URL do prohlížeče a zobrazit výsledky. Zkopírovat a vložit položky ContentUrl do prohlížeče a zobrazit obrázky.

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

            VisualSearchImageBinaryWithCropArea(subscriptionKey);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
                ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);
                
                VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

                Console.WriteLine("\r\nVisual search request with image from URL and crop area combined in knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {

                        foreach(ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            { 
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
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