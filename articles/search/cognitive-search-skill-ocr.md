---
title: Kognitivní dovednosti OCR
titleSuffix: Azure Cognitive Search
description: Extrahujte text z obrazových souborů pomocí optického rozpoznávání znaků (OCR) v kanálu obohacení v Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bdb510113a8d65ac04b54e77158f46d03cccd9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791928"
---
# <a name="ocr-cognitive-skill"></a>Kognitivní dovednosti OCR

Dovednost **Optickérozpoznávání znaků (OCR)** rozpozná tištěný a ručně psaný text v obrazových souborech. Tato dovednost používá modely strojového učení poskytované [počítačové vidění](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) v cognitive services. Dovednost **Rozpoznávání OCR** se mapuje na následující funkce:

+ [Rozhraní API "OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) se používá pro jiné jazyky než angličtinu. 
+ Pro angličtinu se používá nové rozhraní API [pro čtení.](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api)

Dovednost **Rozpoznávání OCR** extrahuje text z obrazových souborů. Mezi podporované formáty souborů patří:

+ . Jpeg
+ . Jpg
+ . Png
+ . Bmp
+ . Gif
+ . Tiff

> [!NOTE]
> Při rozšiřování oboru zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI budete muset [připojit fakturovatelný prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky narůstají při volání API ve službách Cognitive Services a pro extrakci image jako součást fáze prolomení dokumentů v Azure Cognitive Search. Za extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Provádění vestavěných dovedností se účtuje za stávající [cenu průběžných plateb služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsané na [stránce s cenami Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| detectOrientace | Umožňuje automatickou detekci orientace obrazu. <br/> Platné hodnoty: true / false.|
|defaultLanguageCode | <p>  Kód jazyka vstupního textu. Mezi podporované jazyky patří: <br/> zh-Hans (ČínštinaZjednoduše) <br/> zh-Hant (ČínštinaTradiční) <br/>cs (česky) <br/>da (dánština) <br/>nl (holandština) <br/>en (anglicky) <br/>fi (finština)  <br/>fr (francouzština) <br/>  de (Něm.) <br/>el (řecová řečtina) <br/> hu (maď. <br/> to (italsky) <br/>  ja (japonština) <br/> ko (korejština) <br/> pozn.) <br/>   pl (polština) <br/> pt (portugalština) <br/>  ru (Rus.) <br/>  es (španělština) <br/>  sv (Švéd.) <br/>  tr (turečtina) <br/> ar (arabština) <br/> ro (rumunština) <br/> sr-Cyrl (srbština cyrilice) <br/> sr-Latn (SrbskoLatinsko) <br/>  sk (Slovenština). <br/>  unk (Neznámý) <br/><br/> Pokud kód jazyka není zadán nebo má hodnotu null, bude jazyk nastaven na angličtinu. Pokud je jazyk explicitně nastaven na "unk", bude jazyk automaticky rozpoznán. </p> |
|lineEnding | Hodnota, která má být používána mezi jednotlivými zjištěnými čarami. Možné hodnoty: 'Prostor','CarriageReturn','LineFeed'.  Výchozí hodnota je "Mezera" |

Dříve byl parametr s názvem "textExtractionAlgorithm" pro určení, zda dovednost by měla extrahovat "tištěný" nebo "ručně psaný" text.  Tento parametr je zastaralé a již není nutné jako nejnovější algoritmus rozhraní API pro čtení je schopen extrahování obou typů textu najednou.  Pokud definice dovedností již tento parametr obsahuje, není nutné jej odebrat, ale již nebude použit a oba typy textu budou extrahovány do budoucna bez ohledu na to, na co je nastaven.

## <a name="skill-inputs"></a>Vstupy dovedností

| Vstupní název      | Popis                                          |
|---------------|------------------------------------------------------|
| image         | Komplexní typ. V současné době funguje pouze s "/document/normalized_images" pole, ```imageAction``` vyrobené indexerem objektů ```none```blob Azure, pokud je nastavena na jinou hodnotu než . Další informace naleznete v [ukázce.](#sample-output)|


## <a name="skill-outputs"></a>Výstupy dovedností
| Název výstupu     | Popis                   |
|---------------|-------------------------------|
| text          | Prostý text extrahovaný z obrázku.   |
| layoutText    | Komplexní typ, který popisuje extrahovaný text a umístění, kde byl nalezen text.|


## <a name="sample-definition"></a>Definice vzorku

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

## <a name="sample-text-and-layouttext-output"></a>Ukázkový text a rozloženíVýstup textu

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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Ukázka: Sloučení textu extrahovaného z vložených obrazů s obsahem dokumentu.

Běžným případem použití pro sloučení textu je možnost sloučit textovou reprezentaci obrázků (text z dovednosti rozpoznávání OCR nebo titulek obrázku) do pole obsahu dokumentu.

Následující příklad skillset vytvoří *pole merged_text.* Toto pole obsahuje textový obsah dokumentu a text OCRed z každého obrázku vloženého do tohoto dokumentu.

#### <a name="request-body-syntax"></a>Syntaxe textu požadavku
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
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
Výše uvedený příklad skillset předpokládá, že existuje pole normalizované obrázky. Chcete-li generovat toto pole, nastavte konfiguraci *imageAction* v definici indexeru tak, aby *generovala normalizované obrázky,* jak je znázorněno níže:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Viz také
+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Dovednost TextMerger](cognitive-search-skill-textmerger.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření indexeru (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
