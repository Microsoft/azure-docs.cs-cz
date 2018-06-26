---
title: Oblast oříznutí Bing Visual vyhledávání SDK výsledky kurzu | Microsoft Docs
description: Jak používat sadu SDK Bing Visual vyhledávání k získání adres URL bitových kopií podobná oříznout oblasti nahrané bitové kopie.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: fed9bb396d72f140235a2743c1447076606bb87c
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939447"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Kurzu: Oblast oříznutí bitové kopie Bingu Visual vyhledávání SDK a výsledky
Sada Visual vyhledávání SDK zahrnuje možnost pro výběr oblasti obrázku a online Image, které jsou podobné oblasti ořezové větší bitové kopie najít.  Tento příklad určuje oblast oříznutí zobrazující jedna osoba z bitové kopie obsahující několik lidí.  Kód odesílá oblast oříznutí a adresu URL větší bitové kopie a vrátí výsledky, které zahrnují Bing vyhledávání URL a adresy URL podobné bitových kopií najít online.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) získat tento kód spuštěný v systému Windows. (Bezplatná edice Community bude fungovat.)

Musíte mít [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pomocí rozhraní API pro Bing vyhledávání. [Bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) stačí pro tento rychlý start. Je nutné přístupový klíč zadaný při aktivaci bezplatné zkušební verze, nebo může použít klíč placené předplatné z řídicího panelu Azure.

## <a name="application-dependencies"></a>Závislosti aplikací
Pokud chcete nastavit konzolovou aplikaci pomocí sady SDK vyhledávání webové služby Bing, přejděte na možnost Správa balíčků NuGet v Průzkumníku řešení v sadě Visual Studio. Přidejte balíček Microsoft.Azure.CognitiveServices.Search.VisualSearch.

Instalace balíčku NuGet Web vyhledávání SDK nainstaluje taky závislosti, včetně:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Bitové kopie a ořezové oblasti
Následující obrázek znázorňuje tým vyšší vedení společnosti Microsoft.  Pomocí sady SDK Visual vyhledávání, jsme nahrát oblast oříznutí obrázku a najít další Image a webové stránky, které zahrnují entity ve vybrané oblasti větší bitové kopie.  Entita v tomto případě je osoba.

![Microsoft Senior vedení](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Určete oblast, ořezové jako ImageInfo v VisualSearchRequest
Tento příklad používá oblast oříznutí předchozí bitové kopie, který určuje horní vlevo a snížení správné souřadnice podle procenta celého obrázku.  Následující kód vytvoří `ImageInfo` objekt z oblast oříznutí a zatížení `ImageInfo` objektu do `VisualSearchRequest`.  `ImageInfo` Objekt také obsahuje adresu URL obrázku online.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.25, left: (float)0.01, right: (float)0.2);
string imageURL = "http://windowsgeek.lk/wp-content/uploads/2016/04/cxc.png";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Vyhledejte podobná oříznout oblasti bitové kopie
`VisualSearchRequest` Obsahuje ořezové oblasti informace o bitové kopie a jeho adresa URL.  `VisualSearchMethodAsync` Metoda získá výsledky.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Získat adresu URL data z ImageModuleAction
Visual výsledky hledání jsou `ImageTag` objekty.  Jednotlivé značky obsahuje seznam `ImageAction` objekty.  Každý `ImageAction` obsahuje `Data` pole, které je seznam hodnot, které závisí na typu akce:

Různé typy můžete získat následujícím kódem:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Vrátí hotové aplikace:

* Typ akce: PagesIncluding WebSearchURL:
* Typ akce: MoreSizes WebSearchURL:
* Typ akce: VisualSearch WebSearchURL:
* Typ akce: ImageById WebSearchURL: 
* Typ akce: RelatedSearches WebSearchURL:
* Typ akce: Entita -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5380.1
* Typ akce: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx, 5382.1
* Typ akce: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5384.1

Jak je uvedeno v předchozím seznamu, `Entity` `ActionType` obsahuje Bing vyhledávací dotaz, který vrací informace o rozpoznatelném osoby, místní nebo věcí.  `TopicResults` a `ImageResults` typy obsahovat dotazy pro související bitové kopie. Adresy URL v seznamu odkaz na výsledky hledání Bing.


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

## <a name="complete-code"></a>Kompletní kód

Následující kód spustí předchozích příkladech. Odešle obrázek binární v textu požadavku post, spolu s objektem cropArea a vytiskne Bing hledání adresy URL pro každý typ akce. Pokud typ akce PagesIncluding, kód získá ImageObject položky v ImageObject Data.  Data obsahují seznam hodnot, které jsou adresy URL bitové kopie na webových stránkách.  Zkopírujte a vložte výsledné Visual vyhledávání adresy URL do prohlížeče a zobrazit výsledky. Zkopírujte a vložte ContentUrl položky do prohlížeče zobrazíte bitové kopie.

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
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.25, left: (float)0.01, right: (float)0.2);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "http://windowsgeek.lk/wp-content/uploads/2016/04/cxc.png";
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
[Visual hledání odpovědi](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview#the-response)