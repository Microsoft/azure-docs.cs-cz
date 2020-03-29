---
title: Kognitivní dovednosti extrakce dokumentu (náhled)
titleSuffix: Azure Cognitive Search
description: Extrahuje obsah ze souboru v rámci kanálu obohacení. Tato dovednost je v současné době ve verzi Public Preview.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76837727"
---
# <a name="document-extraction-cognitive-skill"></a>Kognitivní dovednosti extrakce dokumentu

> [!IMPORTANT] 
> Tato dovednost je v současné době ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). V současné době neexistuje žádná podpora portálu nebo sady .NET SDK.

Dovednost **Extrakce dokumentu** extrahuje obsah ze souboru v rámci kanálu obohacení. To umožňuje využít krok extrakce dokumentu, který se obvykle stane před spuštěním sady dovedností se soubory, které mohou být generovány jinými dovednostmi.

> [!NOTE]
> Při rozšiřování oboru zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI budete muset [připojit fakturovatelný prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky narůstají při volání API v Cognitive Services a pro extrakci obrazu jako součást fáze prolomení dokumentu v indexování. Za extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Provádění vestavěných dovedností se účtuje za stávající [cenu průběžných plateb služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsány na [stránce s cenami](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Vstupy            | Povolené hodnoty | Popis |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Nastavte `default` na pro extrakci dokumentu ze souborů, které nejsou čistým textem nebo jsonem. Nastavte `text` pro zlepšení výkonu souborů ve formátu prostého textu. Nastavte `json` extrahování strukturovaného obsahu ze souborů json. Pokud `parsingMode` není explicitně definována, `default`bude nastavena na . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Nastavte `contentAndMetadata` extrahování všech metadat a textového obsahu z každého souboru. Nastavte `allMetadata` extrahování pouze [metadat specifických pro obsah](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (například metadata jedinečná pouze pro soubory PNG). Pokud `dataToExtract` není explicitně definována, `contentAndMetadata`bude nastavena na . |
| `configuration` | Viz níže. | Slovník volitelných parametrů, které upravují způsob extrakce dokumentu. Popis podporovaných vlastností konfigurace naleznete v následující tabulce. |

| Parametr konfigurace   | Povolené hodnoty | Popis |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Nastavte `none` na ignorovat vložené obrazy nebo obrazové soubory v datové sadě. Toto nastavení je výchozí. <br/>Pro [analýzu obrazu pomocí kognitivních dovedností](cognitive-search-concept-image-scenarios.md), nastavte tak, aby `generateNormalizedImages` dovednost vytvořit pole normalizované obrazy jako součást dokumentu praskání. Tato akce `parsingMode` vyžaduje, `default` aby `dataToExtract` byla `contentAndMetadata`nastavena na . Normalizovaný obraz odkazuje na další zpracování, které má za následek rovnoměrný výstup obrazu, který je velký a otočen, aby se podpořilo konzistentní vykreslování, když zahrnete obrázky do vizuálních výsledků hledání (například fotografie stejné velikosti v ovládacím prvku grafu, jak je vidět v [ukázce JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Tyto informace jsou generovány pro každý obrázek při použití této možnosti.  <br/>Pokud nastavíte na `generateNormalizedImagePerPage`, soubory PDF budou zpracovány odlišně v tom, že místo extrahování vložených obrazů, každá stránka bude vykreslena jako obrázek a normalizována odpovídajícím způsobem.  Typy souborů, které nejsou ve formátu `generateNormalizedImages` PDF, budou považovány za stejné, jako kdyby byly nastaveny.
| `normalizedImageMaxWidth` | Libovolné celé číslo mezi 50-10000 | Maximální šířka (v obrazových bodech) pro generované normalizované obrazy. Výchozí hodnota je 2000. | 
| `normalizedImageMaxHeight` | Libovolné celé číslo mezi 50-10000 | Maximální výška (v pixelech) pro normalizované obrazy generované. Výchozí hodnota je 2000. |

> [!NOTE]
> Výchozí hodnota 2000 pixelů pro normalizované obrázky maximální šířka a výška je založena na maximálních velikostech podporovaných [dovedností Rozpoznávání OCR](cognitive-search-skill-ocr.md) a [dovedností analýzy obrazu](cognitive-search-skill-image-analysis.md). Dovednost [OCR](cognitive-search-skill-ocr.md) podporuje maximální šířku a výšku 4200 pro neanglické jazyky a 10000 pro angličtinu.  Pokud zvýšíte maximální limity, zpracování může selhat na větších obrázcích v závislosti na definici sady dovedností a jazyku dokumentů. 
## <a name="skill-inputs"></a>Vstupy dovedností

| Vstupní název     | Popis |
|--------------------|-------------|
| file_data | Soubor, ze kterého by měl být obsah extrahován. |

"file_data" vstup musí být objekt definovaný takto:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Tento referenční objekt souboru lze vygenerovat jedním ze 3 způsobů:

 - Nastavení `allowSkillsetToReadFileData` parametru v definici indexeru na hodnotu "true".  Tím vytvoříte `/document/file_data` cestu, která představuje objekt představující původní data souboru stažená ze zdroje dat objektu blob. Tento parametr platí jenom pro data v úložišti objektů Blob.

 - Nastavení `imageAction` parametru v definici indexeru `none`na jinou hodnotu než .  Tím se vytvoří pole obrázků, které se řídí požadovanou konvencí pro `/document/normalized_images/*`vstup do této dovednosti, pokud jsou předány jednotlivě (tj. ).

 - S vlastní dovednost vrátit json objekt definován přesně jako výše.  Parametr `$type` musí být nastaven `file` přesně `data` a parametr musí být základní 64 kódované bajt pole data obsahu souboru.

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu    | Popis |
|--------------|-------------|
| content | Textový obsah dokumentu. |
| normalized_images | Pokud `imageAction` je nastavena na `none`hodnotu jinou , nové *pole normalized_images* bude obsahovat pole obrázků. Další podrobnosti o výstupním formátu každého obrázku naleznete [v dokumentaci pro extrakci obrazu.](cognitive-search-concept-image-scenarios.md) |

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

##  <a name="sample-input"></a>Vstup vzorku

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
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Jak zpracovat a extrahovat informace z obrázků ve scénářích kognitivního vyhledávání](cognitive-search-concept-image-scenarios.md)