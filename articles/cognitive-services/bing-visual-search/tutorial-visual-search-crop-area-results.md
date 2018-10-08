---
title: 'Kurz: Oblast oříznutí obrázku a výsledky – Vizuální vyhledávání Bingu'
description: Jak používat sadu SDK Vizuálního vyhledávání Bingu k získání adres URL obrázků podobných oblasti oříznutí nahraného obrázku.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 66e17c00da898e575bb858dbe16a35d1c44a2780
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226906"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Kurz: Oblast oříznutí obrázku sady SDK Vizuálního vyhledávání Bingu a výsledky
Sada SKD Vizuálního vyhledávání zahrnuje možnost vybrat oblast obrázku a najít online obrázky, které se oblasti oříznutí většího obrázku podobají.  Tento příklad určuje oblast oříznutí zobrazující jednu osobu z obrázku, který obsahuje několik osob.  Kód pošle oblast oříznutí a adresu URL většího obrázku a vrátí výsledky, které zahrnují adresy URL Vyhledávání Bingu a adresy URL podobných obrázků nalezených online.

## <a name="prerequisites"></a>Požadavky

Pokud chcete tento kód spustit na Windows, budete potřebovat [Visual Studio 2017](https://www.visualstudio.com/downloads/). (Bude stačit bezplatná verze Community Edition.)

Musíte mít [účet API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Při aktivaci bezplatné zkušební verze budete potřebovat poskytnutý přístupový klíč nebo můžete použít klíč placeného předplatného z řídicího panelu Azure.

## <a name="application-dependencies"></a>Závislosti aplikace
Pokud chcete nastavit konzolovou aplikaci pomocí sady SDK Vyhledávání na webu Bingu, přejděte v sadě Visual Studio v Průzkumníku řešení na možnost Spravovat balíčky NuGet. Přidejte balíček Microsoft.Azure.CognitiveServices.Search.VisualSearch.

Instalací balíčku NuGet Vyhledávání na webu se také nainstalují závislosti, včetně:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Obrázek a oblast oříznutí
Následující obrázek ukazuje tým nejvyššího vedení Microsoftu.  Pomocí sady SDK Vizuální hledání nahrajeme oblast oříznutí obrázku a najdeme další obrázky a webové stránky, které obsahují entitu ve vybrané oblasti většího obrázku.  V tomto případě je entitou osoba.

![Tým nejvyššího vedení Microsoftu](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Zadání oblasti oříznutí jako ImageInfo ve VisualSearchRequest
Tento příklad používá oblast oříznutí předchozího obrázku, která určuje levou horní a pravou dolní souřadnici podle procent celého obrázku.  Následující kód vytvoří objekt `ImageInfo` z oblasti oříznutí a načte objekt `ImageInfo` do `VisualSearchRequest`.  Objekt `ImageInfo` také zahrnuje adresu URL obrázku online.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Vyhledání obrázků podobných oblasti oříznutí
`VisualSearchRequest` obsahuje informace oblasti oříznutí obrázku a jeho adresu URL.  Metoda `VisualSearchMethodAsync` získá výsledky.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Získání dat adresy URL z ImageModuleAction
Výsledky Vizuálního vyhledávání jsou objekty `ImageTag`.  Každá značka obsahuje seznam objektů `ImageAction`.  Každé `ImageAction` obsahuje pole `Data`, které je seznamem hodnot závislých na typu akce:

Různé typy můžete získat následujícím kódem:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Dokončená aplikace vrátí:

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches  WebSearchURL:
* ActionType: Entity -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1

Jak je vidět v předchozím seznamu, `Entity` `ActionType` obsahuje dotaz Bingu, který vrátí informace o rozpoznatelné osobě, místě nebo věci.  Typy `TopicResults` a `ImageResults` obsahují dotazy pro související obrázky. Adresy URL v seznamu odkazují na výsledky vyhledávání Bingu.


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

## <a name="complete-code"></a>Celý kód

Následující kód spustí předchozí příklady. Odešle binární soubor obrázku v těle požadavku spolu s objektem cropArea a vytiskne adresy URL vyhledávání Bingu pro každý ActionType. Pokud ActionType je PagesIncluding, kód získá položky ImageObject v ImageObject Data.  Data obsahují seznam hodnot, které jsou adresami URL obrázků na webových stránkách.  Zkopírujte a vložte výsledné adresy URL Vizuálního vyhledávání do prohlížeče a zobrazte výsledky. Zkopírujte a vložte položky ContentUrl do prohlížeče a zobrazte obrázky.

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
## <a name="next-steps"></a>Další kroky
[Odpověď Vizuálního vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)