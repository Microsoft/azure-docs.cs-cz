---
title: Obrázek analýzy kognitivního vyhledávání dovednosti – Azure Search
description: Extrahujte text sémantické prostřednictvím image analýza s využitím kognitivních dovedností ImageAnalysis v rozšíření kanálu služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f658f6bb9e66eda13dbf4213e37dcd96b17f76e7
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411013"
---
#   <a name="image-analysis-cognitive-skill"></a>Obrázek analýzy kognitivní dovednosti

**Analýza obrázků** dovednosti extrahuje bohatou sadu funkcí visual na základě obsahu obrázku. Můžete například vygenerovat titulek z bitové kopie, generovat značky nebo identifikovat celebrit a památek. Tato dovednosti pomocí strojového učení modelů poskytované [pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) ve službě Cognitive Services. 

> [!NOTE]
> Od 21. prosince 2018 můžete [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md) s dovednosti Azure Search. To umožňuje spouštění poplatků za využití jeho dovedností. K tomuto datu také začali účtovat pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů dál nabízet bez dalších poplatků.
>
> [Integrované kognitivní dovednosti](cognitive-search-predefined-skills.md) spuštění, se účtuje [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services), na stejné přenosové rychlosti, jako by měl provést úlohu přímo. Extrakce Image je poplatek za Azure Search, v současné době nabízena na ceny verze preview. Podrobnosti najdete v tématu [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) nebo [fakturace](search-sku-tier.md#how-billing-works).

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
                "image":  {
                               "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                               "width": 500,
                               "height": 300,
                               "originalWidth": 5000,  
                               "originalHeight": 3000,
                               "rotationFromOriginal": 90,
                               "contentOffset": 500  
                           }
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
                    "isBwImg": false
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
