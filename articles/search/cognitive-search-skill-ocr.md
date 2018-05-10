---
title: Odborností kognitivní vyhledávání rozpoznávání znaků (Azure Search) | Microsoft Docs
description: Rozbalte text z souborů bitové kopie v obohacení kanál služby Azure Search.
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
ms.openlocfilehash: 84988c815759a726abe93d931f73c284d771a5ba
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="ocr-cognitive-skill"></a>Kognitivní odborností rozpoznávání znaků

**Rozpoznávání znaků** odborností extrahuje text z soubory obrázků. Podporované formáty souborů patří:

+ . JPEG
+ . JPG
+ . PNG
+ . BMP
+ . GIF


## <a name="skill-parameters"></a>Parametry dovedností

Parametry jsou malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| detectOrientation | Umožňuje automatické zjištění orientaci obrázku. <br/> Platné hodnoty: true / false.|
|defaultLanguageCode |  Kód jazyka ze vstupního textu. Mezi podporované jazyky patří: `ar, cs, da, de, en, es, fi, fr, he, hu, it, ko, pt-br, pt`.  Pokud kód jazyka je určena nebo hodnotu null, je jazyk autodetected.|
| textExtractionAlgorithm | "vytištěno" nebo "psané". Algoritmus "ručně psaný" text rozpoznávání rozpoznávání znaků je aktuálně ve verzi preview a podporována pouze v angličtině. |

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Popis                                          |
|---------------|------------------------------------------------------|
| Bitové kopie         | Komplexního typu. Aktuálně jedinou funguje s pole "/ dokumentu/normalized_images" vyprodukované indexeru objektů Blob v Azure při ```imageAction``` je nastaven na ```generateNormalizedImages```. Najdete v článku [ukázka](#sample-output) Další informace.|


## <a name="skill-outputs"></a>Výstupy dovedností
| Název výstupu     | Popis                   |
|---------------|-------------------------------|
| Text          | Prostý text extrahovat z bitové kopie.   |
| layoutText    | Komplexní typ, který popisuje extrahované text a také umístění, kde byl nalezen text.|


## <a name="sample-definition"></a>Ukázka definice

```json
{
    "skills": [
      {
        "description": "Extracts text (plain and structured) from image."
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": null,
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "myText"
          },
          {
            "name": "layoutText",
            "targetName": "myLayoutText"
          }
        ]
      }
    ]
 }
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Ukázkový text a layoutText výstup

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Ukázka: Sloučení text extrahoval z vložené obrázky s obsah dokumentu.

Běžně používá pro Text fúze je možnost sloučení textovou reprezentaci bitových kopií (text ze znalostí rozpoznávání znaků nebo popisek bitové kopie) do pole obsahu dokumentu. 

Následující příklad skillset vytvoří *merged_text* vložených pole tak, aby obsahovala textový obsah dokumentu a také text OCRed z každé bitové kopie v tomto dokumentu. 

#### <a name="request-body-syntax"></a>Syntaxe požadavku textu
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
Výše uvedený příklad skillset předpokládá, že pole normalized-bitové kopie existuje. Chcete-li vygenerovat toto pole, nastavte *imageAction* konfigurace v indexeru definici *generateNormalizedImages* jak je uvedeno níže:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
        }
  }
}
```

## <a name="see-also"></a>Další informace najdete v tématech
+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [TextMerger dovedností](cognitive-search-skill-textmerger.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)