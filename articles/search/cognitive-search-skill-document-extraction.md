---
title: Dovednost pro vyhledávání rozpoznávání dokumentů extrakce dokumentu
titleSuffix: Azure Cognitive Search
description: Extrahuje obsah ze souboru v rámci kanálu pro obohacení.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 8656896fe1a113ab143c43b4d1973e4196c5f087
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512194"
---
# <a name="document-extraction-cognitive-skill"></a>Rozpoznávání extrakce dokumentů – dovednost

Dovednost **extrakce dokumentu** extrahuje obsah ze souboru v rámci kanálu pro obohacení. Díky tomu můžete využít krok extrakce dokumentu, ke kterému obvykle dochází před spuštěním dovednosti se soubory, které mohou být generovány jinými dovednostmi.

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze vytváření dokumentů při indexování. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsány na [stránce s cenami](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. util. DocumentExtractionSkill

## <a name="skill-parameters"></a>Parametry dovednosti

V parametrech jsou rozlišována malá a velká písmena.

| Vstupy            | Povolené hodnoty | Popis |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Nastavte na `default` pro extrakci dokumentů ze souborů, které nejsou čistě textové nebo JSON. Nastavte na `text` pro zlepšení výkonu souborů ve formátu prostého textu. Pro extrakci strukturovaného obsahu ze souborů JSON nastavte `json`. Pokud není `parsingMode` explicitně definované, bude nastaveno na `default`. |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Nastavte na `contentAndMetadata` pro extrakci všech metadat a textových obsahu z každého souboru. Nastavte na `allMetadata` pro extrakci jenom [metadat specifických pro typ obsahu](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (například metadata jsou jedinečná jenom pro soubory PNG). Pokud není `dataToExtract` explicitně definované, bude nastaveno na `contentAndMetadata`. |
| `configuration` | Viz níže. | Slovník volitelných parametrů, který upravuje způsob, jakým se provádí extrakce dokumentu. Popisy podporovaných vlastností konfigurace najdete v následující tabulce. |

| Konfigurační parametr   | Povolené hodnoty | Popis |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Nastavte na `none`, pokud chcete ignorovat vložené obrázky nebo soubory obrázků v sadě dat. Toto je výchozí nastavení. <br/>V případě [analýzy obrázků s využitím odbornosti](cognitive-search-concept-image-scenarios.md)nastavte `generateNormalizedImages`, aby dovednost vytvořila pole normalizovaných imagí jako součást odhalujícího dokumentu. Tato akce vyžaduje, aby se `parsingMode` nastavila na `default` a `dataToExtract` je nastavená na `contentAndMetadata`. Normalizovaná bitová kopie odkazuje na další zpracování, které vede k podpoře konzistentního vykreslování při zahrnutí obrázků do výsledků vizuálního vyhledávání (například fotografie stejné velikosti v ovládacím prvku graf, jak je vidět v JFK). [ Ukázka](https://github.com/Microsoft/AzureSearch_JFK_Files)). Tyto informace jsou vygenerovány pro každý obrázek při použití této možnosti.  <br/>Pokud nastavíte `generateNormalizedImagePerPage`, soubory PDF budou zpracovávány jinak než při extrakci vložených obrázků, každá stránka bude vykreslena jako obrázek a bude odpovídajícím způsobem normalizována.  Typy souborů, které nejsou ve formátu PDF, se budou považovat za stejné, jako kdyby byla nastavena `generateNormalizedImages`.
| `normalizedImageMaxWidth` | Libovolné celé číslo mezi 50-10000 | Maximální šířka (v pixelech) pro vygenerované normalizované bitové kopie. Výchozí hodnota je 2000. | 
| `normalizedImageMaxHeight` | Libovolné celé číslo mezi 50-10000 | Maximální výška (v pixelech) pro vygenerované normalizované bitové kopie. Výchozí hodnota je 2000. |

> [!NOTE]
> Výchozí hodnota 2000 pixelů pro normalizované maximální šířky a výšky obrázků je založena na maximální velikosti podporované [dovedností OCR](cognitive-search-skill-ocr.md) a [dovedností analýzy obrázků](cognitive-search-skill-image-analysis.md). [Dovednost optického rozpoznávání znaků](cognitive-search-skill-ocr.md) podporuje maximální šířku a výšku 4200 pro jiné než anglické jazyky a 10000 pro angličtinu.  Pokud zvýšíte maximální limity, zpracování na větších obrázcích může selhat v závislosti na definici dovednosti a jazyku dokumentů. 
## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu     | Popis |
|--------------------|-------------|
| file_data | Soubor, ze kterého má být extrahován obsah. |

Vstup "file_data" musí být objekt definovaný následujícím způsobem:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Tento objekt odkazu na soubor může být vygenerován jedním ze tří způsobů:

 - Nastavení parametru `allowSkillsetToReadFileData` v definici indexeru na hodnotu "true".  Tím se vytvoří cesta `/document/file_data` objekt reprezentující původní data souborů stažená ze zdroje dat objektu BLOB. Tento parametr se vztahuje pouze na data v úložišti objektů BLOB.

 - Nastavení parametru `imageAction` v definici indexeru na jinou hodnotu než `none`.  Tím se vytvoří pole imagí `/document/normalized_images`, které následují podle požadované konvence pro vstup do této dovednosti, pokud je úspěšná (tj. `/document/normalized_images/*`).

 - Vlastní dovednost vrátí objekt JSON definovaný přesně tak, jak je uvedeno výše.  Parametr `$type` musí být nastaven na přesně `file` a parametr `data` musí být datovým polem s kódováním Base 64 s kódováním obsahu souboru.

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu    | Popis |
|--------------|-------------|
| content | Textový obsah dokumentu |
| normalized_images | Pokud je `imageAction` nastaveno na jinou hodnotu než `none`, bude nové pole *normalized_images* obsahovat pole obrázků. Další podrobnosti o výstupním formátu jednotlivých imagí najdete v [dokumentaci k extrakci imagí](cognitive-search-concept-image-scenarios.md) . |

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
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Vzorový vstup

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

## <a name="see-also"></a>Další informace najdete v tématech

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Postup zpracování a extrakce informací z imagí ve scénářích hledání v rozpoznávání](cognitive-search-concept-image-scenarios.md)