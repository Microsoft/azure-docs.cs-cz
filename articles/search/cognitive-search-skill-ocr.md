---
title: Odborností kognitivní vyhledávání rozpoznávání znaků (Azure Search) | Microsoft Docs
description: Rozbalte text z souborů bitové kopie v obohacení kanál služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 478afe81ed739b98487973eb092ee9cad0aa17fd
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37058899"
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
|defaultLanguageCode | <p>  Kód jazyka ze vstupního textu. Mezi podporované jazyky patří: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>CS (čeština) <br/>da (dánština) <br/>NL (holandština) <br/>en (angličtina) <br/>Fi (finština)  <br/>FR (francouzština) <br/>  Německo (němčina) <br/>El (řečtina) <br/> hu (maďarština) <br/> ho (italština) <br/>  Japonsko (japonština) <br/> Ko (korejština) <br/> NB (norština) <br/>   PL (polština) <br/> PT (portugalština) <br/>  RU (ruština) <br/>  ES (španělština) <br/>  SV (švédština) <br/>  TR (turečtina) <br/> ar (Arabské) <br/> ro (rumunština) <br/> SR-Cyrl (SerbianCyrillic) <br/> SR-Latn (SerbianLatin) <br/>  Sk (slovenština). <br/>  UNK (neznámé) <br/><br/> Pokud kód jazyka je určena nebo hodnotu null, je jazyk autodetected. </p> |
| textExtractionAlgorithm | "vytištěno" nebo "psané". Algoritmus "ručně psaný" text rozpoznávání rozpoznávání znaků je aktuálně ve verzi preview a podporována pouze v angličtině. |

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Popis                                          |
|---------------|------------------------------------------------------|
| image         | Komplexního typu. Aktuálně jedinou funguje s pole "/ dokumentu/normalized_images" vyprodukované indexeru objektů Blob v Azure při ```imageAction``` je nastaven na ```generateNormalizedImages```. Najdete v článku [ukázka](#sample-output) Další informace.|


## <a name="skill-outputs"></a>Výstupy dovedností
| Název výstupu     | Popis                   |
|---------------|-------------------------------|
| text          | Prostý text extrahovat z bitové kopie.   |
| layoutText    | Komplexní typ, který popisuje extrahované text a také umístění, kde byl nalezen text.|


## <a name="sample-definition"></a>Ukázka definice

```json
{
    "skills": [
      {
        "description": "Extracts text (plain and structured) from image.",
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

#### <a name="request-body-syntax"></a>Syntaxe textu požadavku
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
  "parameters":{  
      "configuration":{  
         "dataToExtract":"contentAndMetadata",
         "imageAction":"generateNormalizedImages"
      }
   }
}
```

## <a name="see-also"></a>Další informace najdete v tématech
+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [TextMerger dovedností](cognitive-search-skill-textmerger.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)
+ [Vytvoření Indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
