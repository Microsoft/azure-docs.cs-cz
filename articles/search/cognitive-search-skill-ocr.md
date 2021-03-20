---
title: Dovednost rozpoznávání OCR
titleSuffix: Azure Cognitive Search
description: Extrakce textu ze souborů obrázků pomocí optického rozpoznávání znaků (OCR) v kanálu rozšíření v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 8b6a7c3e05b26cbda80ebf1a3fc0d4fed8255e6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950801"
---
# <a name="ocr-cognitive-skill"></a>Dovednost rozpoznávání OCR

Dovednosti **optického rozpoznávání znaků (OCR)** rozpoznává vytištěný a ručně psaný text v souborech obrázků. Tato dovednost používá v Cognitive Services modely strojového učení, které poskytuje [počítačové zpracování obrazu](../cognitive-services/computer-vision/overview.md) API [v 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) . Dovednosti v **OCR** se mapují na následující funkce:

+ V angličtině, španělštině, němčině, francouzštině, italštině, portugalštině a nizozemštině se používá nové rozhraní API [pro čtení](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) .
+ Pro všechny ostatní jazyky se používá rozhraní ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-api) .

Dovednost **optického rozpoznávání znaků** extrahuje text ze souborů obrázků. Mezi podporované formáty souborů patří:

+ . VE
+ . FORMÁTU
+ . PNG
+ . BMP
+ . VE
+ . TIFF

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů ve službě Azure Kognitivní hledání. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci imagí jsou popsané na [stránce s cenami za Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).


## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Description |
|--------------------|-------------|
| `detectOrientation`   | Umožňuje automatickou detekci orientace obrázku. <br/> Platné hodnoty: true nebo false.|
| `defaultLanguageCode` | <p>   Kód jazyka vstupního textu Mezi podporované jazyky patří: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>cs (Čeština) <br/>da (dánština) <br/>NL (holandština) <br/>EN (angličtina) <br/>Fi (Finština)  <br/>FR (francouzština) <br/>  de (němčina) <br/>El (řečtina) <br/> hu (Maďarština) <br/> IT (italština) <br/>  Ja (japonština) <br/> Ko (korejština) <br/> NB (norština) <br/>   pl (polština) <br/> PT (portugalština) <br/>  ru (ruština) <br/>  ES (španělština) <br/>  SV (švédština) <br/>  TR (turečtina) <br/> ar (arabské písmo) <br/> ro (rumunština) <br/> sr-Cyrl (SerbianCyrillic) <br/> SR-Latn (SerbianLatin) <br/>  SK (slovenština) <br/>  unk (neznámé) <br/><br/> Pokud kód jazyka není zadán nebo je null, jazyk bude nastaven na angličtinu. Pokud je jazyk explicitně nastaven na "unk", jazyk bude automaticky rozpoznán. </p> |
| `lineEnding` | Hodnota, která má být použita mezi každým zjištěným řádkem. Možné hodnoty: "Space", "CarriageReturn", "odřádkování".  Výchozí hodnota je "Space". |

Dříve existoval parametr s názvem "textExtractionAlgorithm", který určuje, zda by měla být tato dovednost extrahována "vytištěna" nebo "rukou" text.  Tento parametr je zastaralý a už není nutný, protože nejnovější algoritmus rozhraní API pro čtení je schopný extrahovat oba typy textu najednou.  Pokud definice dovednosti už tento parametr obsahuje, nemusíte ho odebírat, ale už se nepoužije a oba typy textu se budou extrahovat bez ohledu na to, k čemu je nastavené.

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Description                                          |
|---------------|------------------------------------------------------|
| `image`         | Komplexní typ. V současné době funguje pouze s polem "/Document/normalized_images" vytvořeným indexerem Azure Blob, pokud ```imageAction``` je nastavena na jinou hodnotu než ```none``` . Další informace najdete v [ukázce](#sample-output) .|


## <a name="skill-outputs"></a>Výstupy dovedností
| Název výstupu     | Description                   |
|---------------|-------------------------------|
| `text`            | Z obrázku byl extrahován prostý text.   |
| `layoutText`    | Komplexní typ, který popisuje extrahovaný text a umístění, kde byl text nalezen.|


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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Ukázka: sloučení textu extrahovaného z vložených obrázků s obsahem dokumentu.

Běžný případ použití pro spojování textu je schopnost sloučit textové znázornění obrázků (text z dovednosti optického rozpoznávání znaků nebo titulku obrázku) do pole obsah dokumentu.

Následující příklad dovednosti vytvoří pole *merged_text* . Toto pole obsahuje textový obsah vašeho dokumentu a OCRed text z každého obrázku vloženého do tohoto dokumentu.

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
          "name":"text",
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Výše uvedený příklad dovednosti předpokládá, že existuje pole Normalized-images. Pro vygenerování tohoto pole nastavte konfiguraci *imageAction* v definici indexeru na *generateNormalizedImages* , jak je znázorněno níže:

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
+ [TextMerger dovednost](cognitive-search-skill-textmerger.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Vytvoření indexeru (REST)](/rest/api/searchservice/create-indexer)