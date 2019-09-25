---
title: Dovednost vyhledávání rozpoznávání OCR – Azure Search
description: Extrakce textu ze souborů obrázků pomocí optického rozpoznávání znaků (OCR) v kanálu rozšíření Azure Search.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: da1ca218f7a3d33e6ceb08b3f8d0f632b8b752b7
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265327"
---
# <a name="ocr-cognitive-skill"></a>Dovednost rozpoznávání OCR

Optické rozpoznávání znaků (OCR) rozpoznává vytištěné a ručně psaný text v souborech obrázků. Tato dovednost používá v Cognitive Services modely strojového učení, které poskytuje [počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) . Dovednosti v **OCR** se mapují na následující funkce:

+ Rozhraní API ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) se používá pro jiné jazyky než angličtinu. 
+ V anglickém jazyce se používá nové rozhraní API [pro čtení](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) .

Dovednost **optického rozpoznávání znaků** extrahuje text ze souborů obrázků. Mezi podporované formáty souborů patří:

+ . VE
+ . FORMÁTU
+ .PNG
+ .BMP
+ . VE
+ . TIFF

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů v Azure Search. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsány na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="skill-parameters"></a>Parametry dovednosti

V parametrech jsou rozlišována malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| detectOrientation | Umožňuje automatickou detekci orientace obrázku. <br/> Platné hodnoty: true nebo false.|
|defaultLanguageCode | <p>  Kód jazyka vstupního textu Mezi podporované jazyky patří: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>cs (Čeština) <br/>da (dánština) <br/>NL (holandština) <br/>EN (angličtina) <br/>Fi (Finština)  <br/>FR (francouzština) <br/>  de (němčina) <br/>El (řečtina) <br/> hu (Maďarština) <br/> IT (italština) <br/>  Ja (japonština) <br/> Ko (korejština) <br/> NB (norština) <br/>   pl (polština) <br/> PT (portugalština) <br/>  ru (ruština) <br/>  ES (španělština) <br/>  SV (švédština) <br/>  TR (turečtina) <br/> ar (arabské písmo) <br/> ro (rumunština) <br/> sr-Cyrl (SerbianCyrillic) <br/> SR-Latn (SerbianLatin) <br/>  SK (slovenština). <br/>  unk (neznámé) <br/><br/> Pokud kód jazyka není zadán nebo je null, jazyk bude nastaven na angličtinu. Pokud je jazyk explicitně nastaven na "unk", jazyk bude automaticky rozpoznán. </p> |
|lineEnding | Hodnota, která má být použita mezi každým zjištěným řádkem. Možné hodnoty: ' Space ', ' CarriageReturn ', ' konec '.  Výchozí hodnota je Space. |

Dříve existoval parametr s názvem "textExtractionAlgorithm", který určuje, zda by měla být tato dovednost extrahována "vytištěna" nebo "rukou" text.  Tento parametr je zastaralý a už není nutný, protože nejnovější algoritmus rozhraní API pro čtení je schopný extrahovat oba typy textu najednou.  Pokud definice dovednosti už tento parametr obsahuje, nemusíte ho odebírat, ale už se nepoužije a oba typy textu se budou extrahovat bez ohledu na to, k čemu je nastavené.

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Popis                                          |
|---------------|------------------------------------------------------|
| image         | Komplexní typ. V současné době funguje pouze s polem "/Document/normalized_images" vytvořeným indexerem objektů BLOB v ```imageAction``` Azure, pokud je nastavena na jinou ```none```hodnotu než. Další informace najdete v [ukázce](#sample-output) .|


## <a name="skill-outputs"></a>Výstupy dovedností
| Název výstupu     | Popis                   |
|---------------|-------------------------------|
| text          | Z obrázku byl extrahován prostý text.   |
| layoutText    | Komplexní typ, který popisuje extrahovaný text a umístění, kde byl text nalezen.|


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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Ukázka: Slučuje se text extrahovaný z vložených obrázků s obsahem dokumentu.

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

## <a name="see-also"></a>Viz také:
+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [TextMerger dovednost](cognitive-search-skill-textmerger.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Vytvořit indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
