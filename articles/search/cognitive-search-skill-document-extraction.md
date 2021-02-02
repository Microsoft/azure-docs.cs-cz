---
title: Rozpoznávání extrakce dokumentů – dovednost (Preview)
titleSuffix: Azure Cognitive Search
description: Extrahuje obsah ze souboru v rámci kanálu pro obohacení. Tato dovednost je aktuálně ve verzi Public Preview.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: chalton
ms.openlocfilehash: d6dd2b891cb3bf9ebb5442f541021c20a34ce1a1
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99474842"
---
# <a name="document-extraction-cognitive-skill"></a>Rozpoznávání extrakce dokumentů – dovednost

> [!IMPORTANT] 
> Tato dovednost je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

Dovednost **extrakce dokumentu** extrahuje obsah ze souboru v rámci kanálu pro obohacení. Díky tomu můžete využít krok extrakce dokumentu, ke kterému obvykle dochází před spuštěním dovednosti se soubory, které mohou být generovány jinými dovednostmi.

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze vytváření dokumentů při indexování. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsány na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Vstupy | Povolené hodnoty | Description |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Nastavte na `default` pro extrakci dokumentů ze souborů, které nejsou čistě textové nebo JSON. Nastavte na `text` , aby se zlepšil výkon souborů ve formátu prostého textu. Nastavte na `json` extrakci strukturovaného obsahu ze souborů JSON. Pokud `parsingMode` není definováno explicitně, bude nastaveno na `default` . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Nastavte na `contentAndMetadata` pro extrakci všech metadat a textových obsahu z každého souboru. Nastavte na `allMetadata` k extrakci pouze [vlastností metadat pro daný typ obsahu](search-blob-metadata-properties.md) (například metadata jsou jedinečná pouze pro soubory PNG). Pokud `dataToExtract` není definováno explicitně, bude nastaveno na `contentAndMetadata` . |
| `configuration` | Viz níže. | Slovník volitelných parametrů, který upravuje způsob, jakým se provádí extrakce dokumentu. Popisy podporovaných vlastností konfigurace najdete v následující tabulce. |

| Konfigurační parametr   | Povolené hodnoty | Description |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Nastavte na `none` Ignorovat vložené obrázky nebo soubory obrázků v sadě dat. Tato možnost je výchozí. <br/>V případě [analýzy obrázků s využitím zkušeností s rozpoznáváním](cognitive-search-concept-image-scenarios.md)nastavte, aby `generateNormalizedImages` dovednost vytvořila pole normalizovaných imagí jako součást odhalující dokumentu. Tato akce vyžaduje, aby `parsingMode` byl nastaven na hodnotu `default` a `dataToExtract` byl nastaven na hodnotu `contentAndMetadata` . Normalizovaná bitová kopie odkazuje na další zpracování, které vede k podpoře konzistentního vykreslování při zahrnutí obrázků do výsledků vizuálního vyhledávání (například fotografií stejné velikosti v ovládacím prvku graf, jak je vidět v [ukázce JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Tyto informace jsou vygenerovány pro každý obrázek při použití této možnosti.  <br/>Pokud nastavíte na `generateNormalizedImagePerPage` , soubory PDF budou zpracovávány jinak než při extrakci vložených obrázků, každá stránka bude vygenerována jako obrázek a v odpovídajícím způsobem provede normální nastavení.  Typy souborů, které nejsou v PDF, se budou považovat za stejné, jako kdyby `generateNormalizedImages` byla nastavena.
| `normalizedImageMaxWidth` | Libovolné celé číslo mezi 50-10000 | Maximální šířka (v pixelech) pro vygenerované normalizované bitové kopie. Výchozí hodnota je 2000. | 
| `normalizedImageMaxHeight` | Libovolné celé číslo mezi 50-10000 | Maximální výška (v pixelech) pro vygenerované normalizované bitové kopie. Výchozí hodnota je 2000. |

> [!NOTE]
> Výchozí hodnota 2000 pixelů pro normalizované maximální šířky a výšky obrázků je založena na maximální velikosti podporované [dovedností OCR](cognitive-search-skill-ocr.md) a [dovedností analýzy obrázků](cognitive-search-skill-image-analysis.md). [Dovednost optického rozpoznávání znaků](cognitive-search-skill-ocr.md) podporuje maximální šířku a výšku 4200 pro jiné než anglické jazyky a 10000 pro angličtinu.  Pokud zvýšíte maximální limity, zpracování na větších obrázcích může selhat v závislosti na definici dovednosti a jazyku dokumentů. 
## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu     | Description |
|--------------------|-------------|
| `file_data` | Soubor, ze kterého má být extrahován obsah. |

Vstup "file_data" musí být objekt definovaný následujícím způsobem:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Tento objekt odkazu na soubor může být vygenerován jedním ze tří způsobů:

 - Nastavení `allowSkillsetToReadFileData` parametru v definici indexeru na hodnotu "true".  Tím se vytvoří cesta `/document/file_data` , která představuje objekt reprezentující původní data stažená ze zdroje dat objektu BLOB. Tento parametr se vztahuje pouze na data v úložišti objektů BLOB.

 - Nastavení `imageAction` parametru v definici indexeru na jinou hodnotu než `none` .  Tím se vytvoří pole obrázků, které následují po požadované konvenci pro vstup na tuto dovednost, pokud bylo provedeno jednotlivě (tj. `/document/normalized_images/*` ).

 - Vlastní dovednost vrátí objekt JSON definovaný přesně tak, jak je uvedeno výše.  `$type`Parametr musí být nastaven na přesně `file` a `data` parametr musí být Base 64 kódovaná data pole bajtů obsahu souboru.

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu    | Description |
|--------------|-------------|
| `content` | Textový obsah dokumentu |
| `normalized_images`   | Pokud `imageAction` je hodnota nastavena na jinou hodnotu `none` , pole nový *normalized_images* bude obsahovat pole obrázků. Další podrobnosti o výstupním formátu jednotlivých imagí najdete v [dokumentaci k extrakci imagí](cognitive-search-concept-image-scenarios.md) . |

##  <a name="sample-definition"></a>Definice vzorku

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "extracted_content"
      },
      {
        "name": "normalized_images",
        "targetName": "extracted_normalized_images"
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
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
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
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Postup zpracování a extrakce informací z imagí ve scénářích hledání v rozpoznávání](cognitive-search-concept-image-scenarios.md)