---
title: OCR kognitivního vyhledávání dovednosti – Azure Search
description: Rozbalte text z souborů obrázků v rozšíření kanálu služby Azure Search pomocí optické rozpoznávání znaků (OCR).
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
ms.custom: seodec2018
ms.openlocfilehash: b07c71a9365fca3a2e5d7c837acf689af980afdd
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075817"
---
# <a name="ocr-cognitive-skill"></a>OCR kognitivních dovedností

**OCR** dovednosti extrahuje text z obrazových souborů. Podporované formáty souborů patří:

+ . JPEG
+ . JPG
+ . PNG
+ . BMP
+ . GIF

> [!NOTE]
> Od 21. prosince 2018 se budou moct přidružit dovednosti Azure Search prostředku služeb Cognitive Services. To vám umožní spouštění poplatků za využití jeho dovedností. K tomuto datu také začneme pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů se bude dál nabízet bez dalších poplatků.
>
> Provádění předdefinované dovednosti budou účtovat stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/) . Ceny za extrakce Image se bude účtovat ceny verze preview a je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Přečtěte si [Další](cognitive-search-attach-cognitive-services.md).
>
>  OCR dovedností se mapuje na následujících funkcí služeb cognitive services: Pokud je nastavena na "rukou psaný", textExtractionAlgorithm ["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) slouží funkce.
>  Pokud je nastavena na "Tisk", textExtractionAlgorithm ["OCR"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) funkce se používá pro jiné jazyky než angličtinu. Pro angličtinu nové ["Rozpoznat Text"](../cognitive-services/computer-vision/concept-recognizing-text.md) slouží funkce pro tištěný text.

## <a name="skill-parameters"></a>Parametry dovedností

Parametry rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| detectOrientation | Umožňuje automatické zjištění orientaci obrázku. <br/> Platné hodnoty: true / false.|
|defaultLanguageCode | <p>  Kód jazyka vstupního textu. Mezi podporované jazyky patří: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>CS (čeština) <br/>da (dánština) <br/>Nizozemsko (holandština) <br/>en (v angličtině) <br/>Fi (finština)  <br/>Francie (francouzština) <br/>  Německo (němčina) <br/>El (řečtina) <br/> FU (holandština) <br/> to (italština) <br/>  Japonsko (japonština) <br/> Ko (korejština) <br/> NB (norština) <br/>   PL (polština) <br/> PT (portugalština) <br/>  RU (ruština) <br/>  ES (španělština) <br/>  SV (švédština) <br/>  TR (turečtina) <br/> ar (Arabské písmo) <br/> ro (rumunština) <br/> rozhraní SR-Cyrl (SerbianCyrillic) <br/> rozhraní SR-Latn (SerbianLatin) <br/>  Sk (slovenština). <br/>  UNK (neznámé) <br/><br/> Pokud kód jazyka neurčené nebo mít hodnotu null, nastaví se jazyk na angličtinu. Pokud jazyk explicitně nastavená na "unk", jazyk se automaticky zjištěno. </p> |
| textExtractionAlgorithm | "Tisk" nebo "rukou psaný". Algoritmus OCR rozpoznávání "rukou psaný" text je aktuálně ve verzi preview a podporuje jenom v angličtině. |

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Popis                                          |
|---------------|------------------------------------------------------|
| image         | Komplexního typu. Momentálně se podporuje jenom spolupracuje s poli "/ dokument/normalized_images" vytvářených objektů Blob v Azure indexer při ```imageAction``` je nastavena na ```generateNormalizedImages```. Zobrazit [ukázka](#sample-output) Další informace.|


## <a name="skill-outputs"></a>Výstupy dovedností
| Název výstupního     | Popis                   |
|---------------|-------------------------------|
| text          | Prostý text extrahovaný z image.   |
| layoutText    | Komplexní typ, který popisuje byl extrahován text, stejně jako umístění, ve kterém byl nalezen text.|


## <a name="sample-definition"></a>Ukázková definice

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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Ukázka: Slučování text extrahovaný z vložené obrázky s obsahem dokumentu.

Běžným případem použití pro sloučení textu je možnost sloučit textovou reprezentaci řetězce obrázků (text ze OCR dovedností nebo popisek image) do pole obsahu dokumentu. 

Následující příklad dovednosti vytvoří *merged_text* pole obsahující textový obsah dokumentu, jakož i OCRed text ze všech obrázků, které jsou součástí tohoto dokumentu. 

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
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Výše uvedený příklad dovednosti předpokládá, že existuje pole normalized bitové kopie. Chcete-li generovat toto pole, nastavte *imageAction* konfigurace definice indexeru pro *generateNormalizedImages* jak je znázorněno níže:

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
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření indexeru (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
