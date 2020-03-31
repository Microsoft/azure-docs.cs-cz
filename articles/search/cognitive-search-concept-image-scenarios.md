---
title: Extrakce textu z obrázků
titleSuffix: Azure Cognitive Search
description: Zpracujte a extrahujte text a další informace z ibi v kanálech Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98054060210f55803d6e2811e1f494fd3ff00e48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76838254"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>Jak zpracovat a extrahovat informace z obrázků ve scénářích obohacení ai

Azure Cognitive Search má několik funkcí pro práci s image a obrazové soubory. Během vytváření trhlin v dokumentu můžete použít parametr *imageAction* k extrahování textu z fotografií nebo obrázků obsahujících alfanumerický text, například slovo "STOP" ve znaku stop. Mezi další scénáře patří generování textové reprezentace obrazu, například "pampeliška" pro fotografii pampelišky nebo barva "žlutá". Můžete také extrahovat metadata o obrázku, například jeho velikost.

Tento článek popisuje zpracování obrázků podrobněji a poskytuje pokyny pro práci s obrázky v kanálu obohacení AI.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Získejte normalizované obrázky

Jako součást prolomení dokumentu existuje nová sada parametrů konfigurace indexeru pro zpracování obrazových souborů nebo obrázků vložených do souborů. Tyto parametry se používají k normalizaci obrazů pro další následné zpracování. Normalizace obrazů je činí jednotnějšími. Velikost velkých obrázků se mění na maximální výšku a šířku, aby byly spotřební. U obrazů, které poskytují metadata o orientaci, je otočení obrazu upraveno pro svislé načítání. Úpravy metadat jsou zachyceny ve složitém typu vytvořeném pro každý obrázek. 

Normalizaci obrazu nelze vypnout. Dovednosti, které itrizují nad obrazy, očekávají normalizované obrázky. Povolení normalizace bitové kopie na indexeru vyžaduje, aby skillset být připojeny k tomuto indexeru.

| Parametr konfigurace | Popis |
|--------------------|-------------|
| imageAction   | Nastavte na "none", pokud by při výskytu vložených obrázků nebo obrazových souborů neměla být provedena žádná akce. <br/>Nastavte na "generateNormalizedImages" generovat pole normalizované obrazy jako součást dokumentu praskání.<br/>Nastavte na "generateNormalizedImagePerPage" pro generování pole normalizovaných obrazů, kde pro PDF ve zdroji dat je každá stránka vykreslena na jeden výstupní obraz.  Funkce je stejná jako "generateNormalizedImages" pro typy souborů mimo PDF.<br/>Pro každou možnost, která není "žádná", budou obrázky vystaveny v *poli normalized_images.* <br/>Výchozí hodnota je "žádný". Tato konfigurace je relevantní pouze pro zdroje dat objektů blob, když "dataToExtract" je nastavena na "contentAndMetadata." <br/>Z daného dokumentu bude extrahováno maximálně 1000 obrázků. Pokud je v dokumentu více než 1000 obrázků, bude extrahován prvních 1000 a bude vygenerováno upozornění. |
|  normalizedImageMaxWidth | Maximální šířka (v obrazových bodech) pro generované normalizované obrazy. Výchozí hodnota je 2000. Maximální povolená hodnota je 10000. | 
|  normalizedImageMaxHeight | Maximální výška (v pixelech) pro normalizované obrazy generované. Výchozí hodnota je 2000. Maximální povolená hodnota je 10000.|

> [!NOTE]
> Pokud nastavíte *imageAction* vlastnost na cokoli jiného než "none", nebude možné nastavit *parsingMode* vlastnost na nic jiného než "výchozí".  Můžete nastavit pouze jednu z těchto dvou vlastností na nevýchozí hodnotu v konfiguraci indexeru.

Nastavte parametr **parsingMode** na `json` (chcete-li indexovat každý `jsonArray` objekt blob jako jeden dokument) nebo (pokud objekty BLOB obsahují pole JSON a potřebujete, aby každý prvek pole byl považován za samostatný dokument).

Výchozí hodnota 2000 pixelů pro normalizované obrázky maximální šířka a výška je založena na maximálních velikostech podporovaných [dovedností Rozpoznávání OCR](cognitive-search-skill-ocr.md) a [dovedností analýzy obrazu](cognitive-search-skill-image-analysis.md). Dovednost [OCR](cognitive-search-skill-ocr.md) podporuje maximální šířku a výšku 4200 pro neanglické jazyky a 10000 pro angličtinu.  Pokud zvýšíte maximální limity, zpracování může selhat na větších obrázcích v závislosti na definici sady dovedností a jazyku dokumentů. 

ImageAction v [definici indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer) zadáte takto:

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

Když *imageAction* je nastavena na hodnotu jiné než "none", nové *pole normalized_images* bude obsahovat pole obrázků. Každý obrázek je komplexní typ, který má následující členy:

| Člen obrázku       | Popis                             |
|--------------------|-----------------------------------------|
| data               | BASE64 kódovaný řetězec normalizovaného obrazu ve formátu JPEG.   |
| šířka              | Šířka normalizovaného obrazu v obrazových bodech. |
| height             | Výška normalizovaného obrazu v obrazových bodech. |
| originalWidth      | Původní šířka obrazu před normalizací. |
| originalHeight      | Původní výška obrazu před normalizací. |
| rotationFromOriginal |  Otočení proti směru hodinových ručiček ve stupních, ke kterým došlo k vytvoření normalizovaného obrazu. Hodnota mezi 0 a 360 stupňů. Tento krok přečte metadata z obrázku, který je generován fotoaparátem nebo skenerem. Obvykle násobkem 90 stupňů. |
| contentOffset | Odsazení znaků v poli obsahu, ze kterého byl obraz extrahován. Toto pole je použitelné pouze pro soubory s vloženými obrázky. |
| Pagenumber | Pokud byl obraz extrahován nebo vykreslen z PDF, obsahuje toto pole číslo stránky v PDF, ze kterého byl extrahován nebo vykreslen, počínaje 1.  Pokud obraz nebyl z PDF, bude toto pole 0.  |

 Hodnota vzorku *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Dovednosti související s obrazem

Existují dvě vestavěné kognitivní dovednosti, které pořažují obrázky jako vstup: [OCR](cognitive-search-skill-ocr.md) a [analýza obrázků](cognitive-search-skill-image-analysis.md). 

V současné době tyto dovednosti pracují pouze s obrázky generovanými z kroku prolomení dokumentu. Jako takový je pouze `"/document/normalized_images"`podporovaný vstup .

### <a name="image-analysis-skill"></a>Dovednost analýzy obrázků

[Dovednost Analýza obrazu](cognitive-search-skill-image-analysis.md) extrahuje bohatou sadu vizuálních funkcí založených na obsahu obrazu. Můžete například vygenerovat titulek z obrázku, generovat značky nebo identifikovat celebrity a orientační body.

### <a name="ocr-skill"></a>Dovednost Rozpoznávání OCR

Dovednost [OCR](cognitive-search-skill-ocr.md) extrahuje text z obrazových souborů, jako jsou soubory JPEG, skupina GNG a bitmapy. To může extrahovat text, stejně jako informace o rozložení. Informace o rozložení poskytují ohraničovací rámečky pro každý z identifikovaných řetězců.

## <a name="embedded-image-scenario"></a>Scénář vloženého obrázku

Běžný scénář zahrnuje vytvoření jednoho řetězce obsahujícího veškerý obsah souboru, text i text původu obrázku, provedením následujících kroků:  

1. [Normalized_images výpisu](#get-normalized-images)
1. Spuštění dovednosti Rozpoznávání `"/document/normalized_images"` OCR pomocí jako vstup
1. Sloučí se reprezentace textu těchto obrazů s nezpracovaným textem extrahovaným ze souboru. Dovednost Sloučení [textu](cognitive-search-skill-textmerger.md) můžete použít ke sloučení obou bloků textu do jednoho velkého řetězce.

Následující příklad dovednosti vytvoří *merged_text* pole obsahující textový obsah dokumentu. Obsahuje také text OCRed z každého vloženého obrázku. 

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

Nyní, když máte merged_text pole, můžete mapovat jako prohledávatelné pole v definici indexeru. Veškerý obsah souborů, včetně textu obrázků, bude možné prohledávat.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Vizualizace ohraničovacích rámečků extrahovaného textu

Dalším běžným scénářem je vizualizace informací o rozložení výsledků hledání. Můžete například zvýraznit, kde byla v obraze nalezena část textu jako součást výsledků hledání.

Vzhledem k tomu, že krok OCR se provádí na normalizovaných obrázcích, jsou souřadnice rozvržení v normalizovaném prostoru obrazu. Při zobrazení normalizovaného obrazu není přítomnost souřadnic obecně problémem, ale v některých situacích můžete chtít zobrazit původní obrázek. V takovém případě převeďte každý souřadnicový bod v rozvržení na původní souřadný systém obrazu. 

Jako pomocník, pokud potřebujete transformovat normalizované souřadnice do původního souřadnicového prostoru, můžete použít následující algoritmus:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>Viz také
+ [Vytvořit indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Dovednost analýzy obrázků](cognitive-search-skill-image-analysis.md)
+ [Dovednost Rozpoznávání OCR](cognitive-search-skill-ocr.md)
+ [Dovednost sloučení textu](cognitive-search-skill-textmerger.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Jak mapovat obohacená pole](cognitive-search-output-field-mapping.md)
