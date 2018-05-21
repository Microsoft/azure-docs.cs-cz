---
title: Obrázek Analysis kognitivní vyhledávání odborností (Azure Search) | Microsoft Docs
description: Rozbalte text sémantického prostřednictvím analýzy bitovou kopii pomocí dovedností kognitivní ImageAnalysis v obohacení kanál služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 271f3231078b8842b040509ccf0406ed2415e5ec
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
#   <a name="image-analysis-cognitive-skill"></a>Obrázek kognitivní odborností analýzy

**Image Analysis** odborností extrahuje bohatou sadu funkcí visual podle obsahu bitové kopie. Můžete například vygenerovat popisek z bitové kopie, vygenerovat značky nebo identifikovat celebrit a zajímavá.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Parametry dovedností

Parametry jsou malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| defaultLanguageCode   |  Řetězec označující jazyka, který má vrátit. Služba vrátí výsledky rozpoznávání určený jazyk. Pokud není tento parametr zadán, výchozí hodnota je "en". <br/><br/>Podporované jazyky jsou: <br/>*en* -Angličtina (výchozí) <br/> *zh* – zjednodušená čínština|
|visualFeatures |   Pole řetězce, které označují visual funkce typy, které mají vrátit. Mezi typy platný visual funkce patří:  <ul><li> *kategorie* -rozděluje obsah image podle taxonomii definované v kognitivní služby [dokumentaci](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).</li><li> *značky* -značky bitové kopie s podrobný seznam slova související obsahu bitové kopie.</li><li>*Popis* -popisuje bitovou kopii obsahu s kompletní věta angličtinu.</li><li>*Řezy* -zjistí, pokud tyto řezy jsou k dispozici. Pokud je k dispozici, generuje souřadnice, pohlaví nebo věku.</li><li> *ImageType* -zjistí, pokud je image clipart nebo kreslení čáry.</li><li>   *Barva* -Určuje barva zvýraznění, dominantní barva, a jestli je obrázek černé & bílé.</li><li>*Pro dospělé* -zjistí, pokud je image pornografické ve své podstatě (znázorňuje nahota nebo pohlaví act). Zjistil se také zřejmý sugestivní obsah.</li></ul> Názvy visual funkcí rozlišují malá a velká písmena.|
| Podrobnosti   | Pole řetězců, která určuje, které specifické pro doménu podrobnosti vrátit. Mezi typy platný visual funkce patří: <ul><li>*Celebrit* -identifikuje celebrit, pokud se detekuje v bitové kopii.</li><li>*Zajímavá* -identifikuje zajímavá, pokud se detekuje v bitové kopii.</li></ul>
 |

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Popis                                          |
|---------------|------------------------------------------------------|
| Bitové kopie         | Komplexního typu. Aktuálně jedinou funguje s pole "/ dokumentu/normalized_images" vyprodukované indexeru objektů Blob v Azure při ```imageAction``` je nastaven na ```generateNormalizedImages```. Najdete v článku [ukázka](#sample-output) Další informace.|



##  <a name="sample-definition"></a>Ukázka definice

```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
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

##  <a name="sample-input"></a>Ukázka vstup

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
                                    "faceRectangle": {
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
                    "faceRectangle": {
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


## <a name="error-cases"></a>Případech chyb
V následujících případech chyba jsou extrahovány žádné elementy.

| Kód chyby | Popis |
|------------|-------------|
| NotSupportedLanguage | Zadaný jazyk není podporován. |
| InvalidImageUrl | Adresa URL obrázku je nesprávně naformátovaný nebo není dostupný.|
| InvalidImageFormat | Vstupní data není platný obrázek. |
| InvalidImageSize | Vstupní image je moc velká. |
| NotSupportedVisualFeature  | Funkce zadaný typ není platný. |
| NotSupportedImage | Nepodporované bitovou kopii, například dětská pornografie. |
| InvalidDetails | Nepodporovaný model specifické pro doménu. |

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)
+ [Vytvoření Indexer (REST)](ref-create-indexer.md)
