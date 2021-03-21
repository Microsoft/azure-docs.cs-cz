---
title: Extrakce textu z obrázků
titleSuffix: Azure Cognitive Search
description: Zpracování a extrakce textu a dalších informací z imagí v Azure Kognitivní hledání kanály.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e77bbd6e82d0d4a48b72e13e60b60608f2d7674
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419587"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>Postup zpracování a extrakce informací z imagí ve scénářích obohacení AI

Azure Kognitivní hledání má několik možností pro práci s imagemi a soubory obrázků. Během odhalující dokumentu můžete použít parametr *imageAction* k extrakci textu z fotek nebo obrázků obsahujících alfanumerický text, jako je například slovo "Stop" v znaménku stop. Mezi další scénáře patří generování textové reprezentace obrázku, jako je například "Dandelion" pro fotografii Dandelion nebo barva "žlutá". Můžete také extrahovat metadata o obrázku, jako je jeho velikost.

Tento článek popisuje zpracování imagí podrobněji a poskytuje pokyny pro práci s obrázky v kanálu pro rozšíření AI.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Získat normalizované bitové kopie

V rámci odhalující dokumentu je k dispozici nová sada konfiguračních parametrů indexeru pro zpracování souborů obrázků nebo obrázků vložených do souborů. Tyto parametry slouží k normalizaci imagí pro další zpracování pro příjem dat. Normalizace imagí je díky nim jednotnější. Velikost velkých obrázků se změní na maximální výšku a šířku, aby byly příchodné. Pro obrázky, které poskytují metadata o orientaci, se při vertikálním načítání upraví otočení obrázku. Úpravy metadat jsou zachyceny v komplexním typu vytvořeném pro každý obrázek. 

Normalizaci imagí nelze vypnout. Dovednosti, které iterují na obrázky, očekávají normalizované obrázky. Povolení normalizace imagí u indexeru vyžaduje, aby k tomuto indexeru byl připojen dovednosti.

| Konfigurační parametr | Description |
|--------------------|-------------|
| imageAction   | Nastavte na None, pokud by se při výskytu vložených obrázků nebo souborů obrázků neměla dělat žádná akce. <br/>Nastavte na "generateNormalizedImages", aby se vygenerovalo pole normalizovaných imagí jako součást odhalujícího dokumentu.<br/>Nastavte na "generateNormalizedImagePerPage", chcete-li generovat pole normalizovaných imagí, kde pro soubory PDF ve zdroji dat jsou jednotlivé stránky vykresleny do jedné výstupní image.  Funkce je stejná jako "generateNormalizedImages" pro typy souborů, které nejsou ve formátu PDF.<br/>V případě jakékoli možnosti, která není "none", budou obrázky zobrazeny v poli *normalized_images* . <br/>Výchozí hodnota je None. Tato konfigurace je relevantní pouze pro zdroje dat objektů blob, pokud je "dataToExtract" nastaven na "contentAndMetadata". <br/>Z daného dokumentu bude extrahováno maximálně 1000 imagí. Pokud je v dokumentu více než 1000 obrázků, bude extrahován první 1000 a bude vygenerováno upozornění. |
|  normalizedImageMaxWidth | Maximální šířka (v pixelech) pro vygenerované normalizované bitové kopie. Výchozí hodnota je 2000. Maximální povolená hodnota je 10000. | 
|  normalizedImageMaxHeight | Maximální výška (v pixelech) pro vygenerované normalizované bitové kopie. Výchozí hodnota je 2000. Maximální povolená hodnota je 10000.|

> [!NOTE]
> Pokud nastavíte vlastnost *imageAction* na jinou hodnotu než None, nebudete moci nastavit vlastnost *parsingMode* na jinou hodnotu než "default".  V konfiguraci indexeru můžete nastavit jenom jednu z těchto dvou vlastností na jinou než výchozí hodnotu.

Nastavte parametr **parsingMode** na `json` (Chcete-li indexovat každý objekt BLOB jako jeden dokument) nebo `jsonArray` (Pokud objekty blob obsahují pole JSON a potřebujete, aby každý prvek pole byl považován za samostatný dokument).

Výchozí hodnota 2000 pixelů pro normalizované maximální šířky a výšky obrázků je založena na maximální velikosti podporované [dovedností OCR](cognitive-search-skill-ocr.md) a [dovedností analýzy obrázků](cognitive-search-skill-image-analysis.md). [Dovednost optického rozpoznávání znaků](cognitive-search-skill-ocr.md) podporuje maximální šířku a výšku 4200 pro jiné než anglické jazyky a 10000 pro angličtinu.  Pokud zvýšíte maximální limity, zpracování na větších obrázcích může selhat v závislosti na definici dovednosti a jazyku dokumentů. 

ImageAction v [definici indexeru](/rest/api/searchservice/create-indexer) zadáte následujícím způsobem:

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

Pokud je *imageAction* nastaveno na jinou hodnotu než "none", pole New *normalized_images* bude obsahovat pole obrázků. Každý obrázek je komplexní typ, který má následující členy:

| Člen obrázku       | Description                             |
|--------------------|-----------------------------------------|
| data               | Řetězec s kódováním BASE64 normalizovaného obrázku ve formátu JPEG.   |
| šířka              | Šířka normalizované image v pixelech |
| výška             | Výška normalizované image v pixelech |
| originalWidth      | Původní šířka obrázku před normalizací |
| originalHeight      | Původní výška obrázku před normalizací |
| rotationFromOriginal |  Rotace proti směru hodinových ručiček ve stupních, ve kterých došlo k vytvoření normalizované bitové kopie. Hodnota v rozmezí 0 stupňů až 360 stupňů. Tento krok přečte metadata z image, která je vygenerovaná fotoaparátem nebo skenerem. Obvykle násobek 90 stupňů. |
| contentOffset | Posun znaku v rámci pole Content, ze kterého byl obrázek extrahován. Toto pole platí pouze pro soubory s vloženými obrázky. |
| pageNumber | Pokud byl obrázek extrahován nebo vykreslen z formátu PDF, toto pole obsahuje číslo stránky v souboru PDF, ze kterého byl extrahován nebo vykreslen, od 1.  Pokud obrázek nebyl z PDF, bude toto pole 0.  |

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
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Dovednosti související s obrázkem

K dispozici jsou dva integrované příhlasné dovednosti, které přijímají obrázky jako vstup: analýza [OCR](cognitive-search-skill-ocr.md) a [obrázku](cognitive-search-skill-image-analysis.md). 

V současné době tyto dovednosti fungují jenom s obrázky generovanými z kroku pro trhliny dokumentů. V takovém případě je jediným podporovaným vstupem `"/document/normalized_images"` .

### <a name="image-analysis-skill"></a>Dovednost analýzy obrázků

[Dovednost analýzy obrázků](cognitive-search-skill-image-analysis.md) extrahuje bohatou sadu vizuálních funkcí založenou na obsahu obrázku. Můžete například vygenerovat titulek z obrázku, generovat značky nebo identifikovat celebrit a orientačních bodů.

### <a name="ocr-skill"></a>Dovednost OCR

[Dovednost optického rozpoznávání znaků](cognitive-search-skill-ocr.md) extrahuje text ze souborů obrázků, jako jsou JPGs, pngs a bitmapy. Může extrahovat text i informace o rozložení. Informace o rozložení poskytují ohraničující pole pro každý identifikovaný řetězec.

## <a name="embedded-image-scenario"></a>Scénář vložené image

Běžný scénář zahrnuje vytvoření jednoho řetězce obsahujícího celý obsah souboru, textu textu i obrázku, a to provedením následujících kroků:  

1. [Extrahovat normalized_images](#get-normalized-images)
1. Spustit dovednosti optického rozpoznávání znaků pomocí `"/document/normalized_images"` as Input
1. Sloučí text reprezentující obrázky s nezpracovaným textem extrahovaným ze souboru. Dovednost [sloučení textu](cognitive-search-skill-textmerger.md) můžete použít k sloučení obou textových bloků do jednoho velkého řetězce.

Následující příklad dovednosti vytvoří pole *merged_text* obsahující textový obsah dokumentu. Zahrnuje také text OCRed z každého vloženého obrázku. 

#### <a name="request-body-syntax"></a>Syntaxe textu žádosti
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

Teď, když máte merged_text pole, můžete ho v definici indexeru namapovat jako vyhledávací pole. Veškerý obsah souborů, včetně textu obrázků, bude možné prohledávat.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Vizualizace ohraničujících polí extrahovaných textů

Dalším běžným scénářem je vizualizace informací o rozložení výsledků hledání. Například můžete chtít zvýraznit, kde byl nějaký text nalezen v obrázku jako součást výsledků hledání.

Vzhledem k tomu, že je krok OCR proveden na normalizovaných obrázcích, jsou souřadnice rozložení v normalizovaném prostoru obrázku. Při zobrazení normalizované bitové kopie není přítomnost souřadnic obecně problémem, ale v některých situacích může být vhodné zobrazit původní obrázek. V takovém případě převeďte všechny body souřadnic v rozložení na původní souřadnicový systém obrázku. 

Pokud potřebujete transformovat normalizované souřadnice na původní souřadnicový prostor, můžete jako pomocníka použít následující algoritmus:

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
## <a name="passing-images-to-custom-skills"></a>Předávání imagí vlastním dovednostím

Pro scénáře, kdy potřebujete vlastní dovednost pro práci na obrázcích, můžete předat obrázky vlastní dovednosti a nechat ji vracet text nebo obrázky. Pracovní postup, který zpracovává ukázkovou bitovou kopii [Pythonu](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing) . Následující dovednosti je z ukázky.

Následující dovednosti přebírá normalizovanou bitovou kopii (získanou během odhalující dokumentu) a produkuje výstupy řezů obrázku.

#### <a name="sample-skillset"></a>Ukázka dovednosti
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
          "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
          "name": "ImageSkill",
          "description": "Segment Images",
          "context": "/document/normalized_images/*",
          "uri": "https://your.custom.skill.url",
          "httpMethod": "POST",
          "timeout": "PT30S",
          "batchSize": 100,
          "degreeOfParallelism": 1,
          "inputs": [
            {
              "name": "image",
              "source": "/document/normalized_images/*"
            }
          ],
          "outputs": [
            {
              "name": "slices",
              "targetName": "slices"
            }
          ],
          "httpHeaders": {}
        }
  ]
}
```

#### <a name="custom-skill"></a>Vlastní dovednost

Vlastní dovednosti jsou pro dovednosti vnější. V tomto případě se jedná o kód Pythonu, který první smyčka důkladně prochází dávkou záznamů žádostí ve formátu vlastní dovednosti a pak převede řetězec kódovaný ve formátu base64 na obrázek.

```python
# deserialize the request, for each item in the batch
for value in values:
  data = value['data']
  base64String = data["image"]["data"]
  base64Bytes = base64String.encode('utf-8')
  inputBytes = base64.b64decode(base64Bytes)
  # Use numpy to convert the string to an image
  jpg_as_np = np.frombuffer(inputBytes, dtype=np.uint8)
  # you now have an image to work with
```
Podobně jako vrácení obrázku vrátí řetězec kódovaný v kódování Base64 v rámci objektu JSON s `$type` vlastností `file` .

```python
def base64EncodeImage(image):
    is_success, im_buf_arr = cv2.imencode(".jpg", image)
    byte_im = im_buf_arr.tobytes()
    base64Bytes = base64.b64encode(byte_im)
    base64String = base64Bytes.decode('utf-8')
    return base64String

 base64String = base64EncodeImage(jpg_as_np)
 result = { 
  "$type": "file", 
  "data": base64String 
}
```

## <a name="see-also"></a>Viz také
+ [Vytvořit indexer (REST)](/rest/api/searchservice/create-indexer)
+ [Dovednost analýzy obrázků](cognitive-search-skill-image-analysis.md)
+ [Dovednost OCR](cognitive-search-skill-ocr.md)
+ [Dovednost sloučení textu](cognitive-search-skill-textmerger.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Jak mapovat obohacená pole](cognitive-search-output-field-mapping.md)
+ [Postup předávání imagí vlastním dovednostím](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing)
