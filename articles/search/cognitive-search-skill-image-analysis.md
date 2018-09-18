---
title: Obrázek analýzy kognitivního vyhledávání dovedností (Azure Search) | Dokumentace Microsoftu
description: Extrahujte text sémantické prostřednictvím image analýza s využitím kognitivních dovedností ImageAnalysis v rozšíření kanálu služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 7b7e21e3e5ad02d74a2ae39e37c9dd8086c2cdd6
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729465"
---
#   <a name="image-analysis-cognitive-skill"></a>Obrázek analýzy kognitivní dovednosti

**Analýza obrázků** dovednosti extrahuje bohatou sadu funkcí visual na základě obsahu obrázku. Můžete například vygenerovat titulek z bitové kopie, generovat značky nebo identifikovat celebrit a památek.

> [!NOTE]
> Cognitive Search je ve veřejné verzi Preview. Spuštění dovednosti a extrakce image a normalizace se momentálně nabízí zdarma. Později cenách za tyto funkce bude oznámena. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Parametry dovedností

Parametry rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| defaultLanguageCode   |  Řetězec označující jazyk, který chcete vrátit. Tato služba vrátí výsledky rozpoznávání do zadaného jazyku. Pokud není tento parametr zadán, výchozí hodnota je "en". <br/><br/>Podporované jazyky jsou: <br/>*cs* -Angličtina (výchozí) <br/> *zh* – zjednodušená čínština|
|visualFeatures |   Pole řetězce, které označují typy vizuální funkce vrátit. Platný vizuální funkce patří:  <ul><li> *kategorie* – rozděluje obsah image podle taxonomie definované ve službě Cognitive Services [dokumentaci](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).</li><li> *značky* – značky bitová kopie se podrobný seznam slov, související s obsahem obrazu.</li><li>*Popis* -popisuje obsah s kompletní věta anglické image.</li><li>*Tváří* -rozpozná, pokud jsou k dispozici tváří. Pokud jsou k dispozici, generuje souřadnice, pohlaví nebo věku.</li><li> *ImageType* – zjistí, jestli obrázek je klipart nebo perokresba.</li><li>   *Barva* -Určuje barvu motivu, dominantní barva, a určuje, zda je bitová kopie černé černobílý.</li><li>*Pro dospělé* – zjistí, zda se má obrázek pornografický ze své podstaty (znázorňuje nahota nebo act pohlaví). Zjistil se také sexuálně sugestivní obsah.</li></ul> Názvy funkcí visual jsou malá a velká písmena.|
| Podrobnosti   | Pole řetězce, které označují, které specifického pro doménu podrobnosti k vrácení. Platný vizuální funkce patří: <ul><li>*Celebrit* -identifikuje celebrit, pokud se zjistí na obrázku.</li><li>*Zajímavá* -identifikuje památek, pokud se zjistí na obrázku.</li></ul>
 |

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Popis                                          |
|---------------|------------------------------------------------------|
| image         | Komplexního typu. Momentálně se podporuje jenom spolupracuje s poli "/ dokument/normalized_images" vytvářených objektů Blob v Azure indexer při ```imageAction``` je nastavena na ```generateNormalizedImages```. Zobrazit [ukázka](#sample-output) Další informace.|



##  <a name="sample-definition"></a>Ukázková definice

```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "context": "/document/normalized_images/*",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
        }
    ]
}
```

##  <a name="sample-input"></a>Ukázkový vstup

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "url": "https://storagesample.blob.core.windows.net/sample-container/image.jpg"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Ukázkový výstup

```json
{
    "values": [
      {
        "recordId": "1",
            "data": {
                "categories": [
           {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                "name": "people_",
                        "score": 0.83984375,
                "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceBoundingBox": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                    "faceBoundingBox": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBWImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
           }
      }
    ]
}
```


## <a name="error-cases"></a>Případy chyb
V následujících případech chyba jsou extrahovány žádné elementy.

| Kód chyby | Popis |
|------------|-------------|
| NotSupportedLanguage | Zadaný jazyk není podporován. |
| InvalidImageUrl | Adresa URL obrázku je chybně formátovaná nebo není přístupný.|
| InvalidImageFormat | Vstupní data netvoří platnou bitovou kopií. |
| InvalidImageSize | Vstupního obrázku je příliš velký. |
| NotSupportedVisualFeature  | Typ zadaná funkce není platný. |
| NotSupportedImage | Nepodporovaný obrázek, například dětskou pornografii. |
| InvalidDetails | Nepodporovaný model specifického pro doménu. |

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření indexeru (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
