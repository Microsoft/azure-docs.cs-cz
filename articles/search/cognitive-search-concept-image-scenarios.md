---
title: Zpracování a rozbalte text z bitových kopií ve službě Azure Search | Microsoft Docs
description: Proces a extrahování textu a dalších informací z bitové kopie v kognitivní vyhledávání ve službě Azure Search kanály.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: c58e731f6b8c86a0b7d6f2500d81077904b2f5ef
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Jak zpracovat a extrahovat informace z bitových kopií ve scénářích kognitivní vyhledávání

Kognitivní vyhledávání má několik možností pro práci s obrázky a soubory obrázků. Během hádání toho dokumentu, můžete použít *imageAction* parametru rozbalte text z fotografie nebo obrázky obsahující alfanumerické text, například v značku stop je slovo "STOP". Další scénáře patří generování reprezentace textu obrázku, jako je například "dandelion" pro fotografie dandelion nebo barvu "žlutý". Můžete také extrahovat metadata o bitovou kopii, například jeho velikost.

Tento článek vysvětluje image zpracování podrobněji a poskytuje pokyny pro práci s obrázky v kanálu kognitivní vyhledávání.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Získat normalizovaný bitové kopie

Jako součást hádání toho dokumentu jsou novou sadu parametrů konfigurace indexeru pro zpracování souborů bitové kopie nebo bitové kopie v souborech. Tyto parametry se použít k normalizaci bitových kopií pro další zpracování příjmu dat. Normalizace bitové kopie, umožní jejich jednotnější. Maximální výška a šířka tak, aby byly použití ke změně velikosti velkých obrázků. Pro poskytování metadat na orientaci Image image otočení upravují pro svislé načítání. Úpravy metadata zachyceny v komplexní typ vytvořené pro každé bitové kopie. 

Nelze vypnout normalizaci bitové kopie. Dovedností, které provádějí iterace bitové kopie očekávat normalizovaný bitové kopie.

| Parametr konfigurace | Popis |
|--------------------|-------------|
| imageAction   | Nastavte na "žádný" Pokud žádná akce, je třeba při došlo k vložené obrázky nebo soubory obrázků. <br/>Nastavte na "generateNormalizedImages" ke generování pole bitových kopií normalizovaných jako součást krakování dokumentu. Tyto bitové kopie k dispozici v *normalized_images* pole. <br/>Výchozí hodnota je "none." Tato konfigurace je pouze relevantní do objektu blob datových zdrojů, "dataToExtract" je nastavena na "contentAndMetadata." |
|  normalizedImageMaxWidth | Maximální šířka (v pixelech) pro normalizovaný obrázky vygenerována. Výchozí hodnota je 2000.|
|  normalizedImageMaxHeight | Maximální výška (v pixelech) pro normalizovaný obrázky vygenerována. Výchozí hodnota je 2000.|

> [!NOTE]
> Pokud nastavíte *imageAction* vlastnost na jakoukoli jinou hodnotu než "žádný", nebude možné nastavit *parsingMode* vlastnost na jakoukoli jinou hodnotu než "Výchozí".  Pouze jednu z těchto dvou vlastností může nastavit v konfiguraci indexer na jiné než výchozí hodnotu.

Výchozí 2000 pixelů pro bitových kopií normalizovaných maximální šířku a výšku je založena na maximální velikosti podporované [rozpoznávání znaků odborností](cognitive-search-skill-ocr.md) a [image analysis odborností](cognitive-search-skill-image-analysis.md). Pokud můžete zvýšit maximální limit, může na větší bitové kopie nezdaří zpracování.


Zadejte imageAction ve vaší [indexer definice](ref-create-indexer.md) následujícím způsobem:

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

Když *imageAction* je nastaven na "generateNormalizedImages", nové *normalized_images* pole bude obsahovat pole bitové kopie. Každé bitové kopie je komplexní typ, který má následující členy:

| Člen bitové kopie       | Popis                             |
|--------------------|-----------------------------------------|
| data               | Řetězec normalizovaný bitové kopie ve formátu JPEG kódování BASE64.   |
| Šířka              | Šířka normalizovaný obrázku v pixelech. |
| Výška             | Výška normalizovaný obrázku v pixelech. |
| originalWidth      | Původní šířku obrázku před normalizace. |
| originalHeight      | Původní výšku obrázku před normalizace. |
| rotationFromOriginal |  Proti směru hodinových ručiček otočení ve stupních, chcete-li vytvořit bitovou kopii normalizovaný došlo k chybě. Hodnota mezi 0 stupňů do 360 stupňů. Tento krok čte metadata z obrázku, který je generován fotoaparátu nebo skener. Obvykle více 90 stupňů. |
| parametr ContentOffset rovný |Odsazení znaku v rámci pole obsahu, které jste extrahovali bitovou kopii z. Toto pole platí pouze pro soubory s vložené obrázky. |

 Ukázková hodnota *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500  
  }
]
```

## <a name="image-related-skills"></a>Související s imagí dovednosti

Existují dvě předdefinované kognitivní dovedností, které trvat bitové kopie jako vstup: [rozpoznávání znaků](cognitive-search-skill-ocr.md) a [Image Analysis](cognitive-search-skill-image-analysis.md). 

V současné době tyto znalosti pracovat pouze s obrázky generované z kroku odhalení dokumentu. Jako takový pouze podporované vstup je `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Analýza dovedností bitové kopie

[Image Analysis odborností](cognitive-search-skill-image-analysis.md) extrahuje bohatou sadu funkcí visual podle obsahu bitové kopie. Můžete například vygenerovat popisek z bitové kopie, vygenerovat značky nebo identifikovat celebrit a zajímavá.

### <a name="ocr-skill"></a>Odborností rozpoznávání znaků

[Rozpoznávání znaků odborností](cognitive-search-skill-ocr.md) extrahuje text z soubory obrázků, jako je například formátu JPG využívá formát PNG a rastrové obrázky. Může extrahovat text a také informace o rozložení. Informace o rozvržení poskytuje ohraničující políčka pro každou z řetězce identifikovat.

Rozpoznávání znaků odborností umožňuje vybrat algoritmus použitý pro zjišťování text ve vaší bitové kopie. Aktuálně podporuje dva algoritmy, jednu pro text na tištěných a druhou pro ručně psaný text.

## <a name="embedded-image-scenario"></a>Scénář vložený obrázek

Běžný scénář zahrnuje vytvoření jeden řetězec obsahující všechny obsah souboru, text a text počátek obrázku, provedením následujících kroků:  

1. [Extrahování normalized_images](#get-normalized-images)
1. Spustí pomocí dovedností rozpoznávání znaků `"/document/normalized_images"` jako vstup
1. Sloučení textovou reprezentaci hodnoty tyto bitové kopie s nezpracovaný text extrahována ze souboru. Můžete použít [Text sloučení](cognitive-search-skill-textmerger.md) dovednosti pro konsolidaci obou bloky text do jednoho velkého řetězce.

Následující příklad skillset vytvoří *merged_text* pole obsahující textový obsah dokumentu. Zahrnuje také OCRed text ze všech vložené obrázky. 

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

Teď, když máte merged_text pole, může se mapovat jako prohledávatelné pole v definici vaší indexer. Veškerý obsah souborů, včetně textu bitové kopie, bude vyhledávat.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Vizualizace ohraničujícího polí extrahované textu

Další z typických možností je vizualizace informace o rozvržení výsledky hledání. Například můžete chtít zvýrazněte, kde byla nalezena část textu v obraze jako součást výsledků hledání.

Vzhledem k tomu, že krok rozpoznávání znaků provádí na normalizovaný bitové kopie, rozložení souřadnice jsou uváděny v prostoru normalizovaný bitové kopie. Při zobrazení normalizovaný bitové kopie, přítomnost souřadnice se obecně nejedná o problém, ale v některých případech můžete chtít zobrazit původní bitové kopie. V takovém případě každý souřadnic bodů v rozložení převeďte na původní bitové kopie souřadnicový systém. 

Pokud potřebujete transformace normalizovaný souřadnice k původní souřadnicového prostoru, můžete jako pomocné rutiny, použít následujícím algoritmu:

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

## <a name="see-also"></a>Další informace najdete v tématech
+ [Vytvoření indexer (REST)](ref-create-indexer.md)
+ [Analýza dovedností bitové kopie](cognitive-search-skill-image-analysis.md)
+ [Odborností rozpoznávání znaků](cognitive-search-skill-ocr.md)
+ [Sloučení odborností textu](cognitive-search-skill-textmerger.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)
+ [Postup mapování provádět rozšířené pole](cognitive-search-output-field-mapping.md)
