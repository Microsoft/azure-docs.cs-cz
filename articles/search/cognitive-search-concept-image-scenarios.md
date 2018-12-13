---
title: Zpracování a extrahování text z obrázků v kognitivního vyhledávání – Azure Search
description: Zpracování a extrahování textu a dalších informací z obrázků v cognitive hledání kanálů ve službě Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: b4829b0da656c648db732b2e7564de6db8fbf2eb
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312608"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Postupy zpracování a extrahování informací z obrázků ve scénářích kognitivního vyhledávání

Kognitivní vyhledávání má několik funkcí pro práci s obrázky a soubory obrázků. Během hádání dokumentu, můžete použít *imageAction* parametr k extrakci textu z fotografie nebo obrázků obsahující text alfanumerické znaky, jako je například v znaménko stop slovo "STOP". Další scénáře zahrnují generování textové vyjádření image, jako je například "dandelion" fotografie dandelion nebo barvu "žlutý". Lze rovněž extrahovat metadata o imagi, jako je například jeho velikost.

Tento článek popisuje podrobněji pro zpracování obrázků a poskytuje pokyny pro práci s obrázky v kanálu kognitivního vyhledávání.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Získání normalizované obrázků

Jako součást hádání dokumentu jsou nová sada parametry konfigurace indexeru pro zpracování souborů obrázků nebo Image vložené v souborech. Normalizace bitových kopií pro další zpracování příjmu dat se používají tyto parametry. Normalizace imagí je mezi nimi vlastně jednotnější. Velké obrázky se změněnou velikostí maximální výšku a šířku tak, aby byly použitelné. Pro Image poskytování metadat na orientaci otočení obrázku se upraví pro vertikální načítání. Úpravy metadat jsou zachyceny v komplexní typ vytvoří pro každý obrázek. 

Normalizace image se nedá vypnout. Dovednosti, které iterují v imagí očekávat normalizované imagí.

| Parametr konfigurace | Popis |
|--------------------|-------------|
| imageAction   | Nastavte na "none", pokud žádná akce by měla být provedeny, když došlo k vložené obrázky nebo soubory obrázků. <br/>Nastavte na "generateNormalizedImages" generovat pole normalizované imagí jako součást analýzy dokumentu. Tyto Image se zveřejní v *normalized_images* pole. <br/>Výchozí hodnota je "none". Tato konfigurace je pouze relevantní do objektu blob zdroje dat, když "dataToExtract" je nastavena na "contentAndMetadata." |
|  normalizedImageMaxWidth | Maximální šířku (v pixelech) normalizované obrázky generované. Výchozí hodnota je 2000.|
|  normalizedImageMaxHeight | Maximální výšku (v pixelech) normalizované obrázky generované. Výchozí hodnota je 2000.|

> [!NOTE]
> Pokud jste nastavili *imageAction* vlastnost na jinou hodnotu než "none", nebude možné nastavit *parsingMode* vlastnost na jinou hodnotu než "Výchozí".  Pouze jednu z těchto dvou vlastností může nastavit vaše konfigurace indexeru na jiné než výchozí hodnotu.

Nastavte **parsingMode** parametr `json` (k indexování jednotlivých objektů blob jako jeden dokument) nebo `jsonArray` (Pokud objektů BLOB obsahovat pole JSON a je třeba každý prvek pole považován za samostatný dokumentu).

Výchozí hodnota pro normalizované imagí maximální šířku a výšku v pixelech 2000 je založen na maximální velikosti podporované [OCR dovednosti](cognitive-search-skill-ocr.md) a [obrázku analýzy dovednosti](cognitive-search-skill-image-analysis.md). Pokud zvýšíte na povolené maximum, zpracování může dojít při jeho větší obrázky.


Zadejte imageAction ve vašich [definice indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer) následujícím způsobem:

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

Když *imageAction* je nastavena na "generateNormalizedImages" nové *normalized_images* bude pole obsahovat celou řadu imagí. Každé image je komplexní typ, který má následující členy:

| Člen bitové kopie       | Popis                             |
|--------------------|-----------------------------------------|
| data               | Řetězec normalizované obrázku ve formátu JPEG s kódováním BASE64.   |
| Šířka              | Šířka normalizované obrázku v pixelech. |
| Výška             | Výška normalizované obrázku v pixelech. |
| originalWidth      | Původní šířka obrázku před normalizace. |
| originalHeight      | Původní výška obrázku před normalizace. |
| rotationFromOriginal |  Proti směru hodinových ručiček otočení ve stupních, ke kterým došlo k vytvoření této image normalizovaná. Hodnotu v rozmezí 0 stupňů až 360 stupňů. Tento krok načte metadata z obrázku, který je generován fotoaparát nebo skeneru. Obvykle více 90 stupňů. |
| parametr ContentOffset rovný |Odsazení znaku v obsahu pole, ve kterém se image získané z. Toto pole platí pouze pro soubory s vložené obrázky. |

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

Existují dvě integrované kognitivní dovednosti, které jako vstup přijímat imagí: [OCR](cognitive-search-skill-ocr.md) a [Analýza obrázků](cognitive-search-skill-image-analysis.md). 

V současné době předvedou dovednosti pracovat pouze s obrázky generované z kroku odhalující dokumentu. V důsledku toho je pouze podporované vstupní `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Obrázek analýzy dovedností

[Analýza obrázků dovednosti](cognitive-search-skill-image-analysis.md) extrahuje bohatou sadu funkcí visual na základě obsahu obrázku. Můžete například vygenerovat titulek z bitové kopie, generovat značky nebo identifikovat celebrit a památek.

### <a name="ocr-skill"></a>OCR dovedností

[OCR dovednosti](cognitive-search-skill-ocr.md) extrahuje text z obrazových souborů, jako je například formátu JPG využívá, PNG a rastrových obrázků. Může extrahovat text a také informace o rozložení. Informace o rozložení poskytuje ohraničující pole pro každý z řetězců identifikovat.

OCR dovednosti můžete vybrat algoritmus použije pro zjišťování text vašich imagí. Aktuálně podporuje dva algoritmy, jeden pro tištěný text a druhý pro rukou psaný text.

## <a name="embedded-image-scenario"></a>Scénář vložený obrázek

Běžný scénář zahrnuje vytvoření jeden řetězec obsahující všechny obsah souboru, text a počátek obrázku textu, podle následujících kroků:  

1. [Extrahovat normalized_images](#get-normalized-images)
1. Spuštění pomocí technologie OCR dovednosti `"/document/normalized_images"` jako vstup
1. Sloučit textová reprezentace těchto imagí s nezpracovaný text extrahovány ze souboru. Můžete použít [sloučení textu](cognitive-search-skill-textmerger.md) dovednosti ke sloučení obou textové bloky dat do jednoho velkého řetězce.

Následující příklad dovednosti vytvoří *merged_text* pole obsahující textový obsah dokumentu. Zahrnuje také OCRed text ze všech vložené obrázky. 

#### <a name="request-body-syntax"></a>Syntaxe tělo požadavku
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

Teď, když máte merged_text pole, můžete ji namapovat jako prohledávatelném poli v definici indexeru. Veškerý obsah souborů, včetně textu, obrázků, bude možné prohledávat.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Vizualizujte ohraničující pole byl extrahován text

Další z typických možností je vizualizace informace o rozložení výsledků hledání. Například můžete chtít zvýraznit, kde byla nalezena část textu v obrázku jako součást výsledků hledání.

Protože OCR krok se provádí u zobrazení normalizovaná, rozložení souřadnice jsou uváděny v prostoru normalizované image. Při zobrazení normalizovaná bitové kopie, přítomnost souřadnice většinou není problém, ale v některých případech můžete chtít zobrazit původní bitové kopie. V tomto případě každý souřadnice bodů v rozložení převeďte na původní systém souřadnic bitové kopie. 

Pokud potřebujete transformovat normalizované souřadnice, kde původní souřadnicového prostoru, můžete jako pomocné rutiny, použít následující požadovaný algoritmus:

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
+ [Vytvoření indexeru (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Analýza obrázku dovedností](cognitive-search-skill-image-analysis.md)
+ [OCR dovedností](cognitive-search-skill-ocr.md)
+ [Dovednosti sloučení textu](cognitive-search-skill-textmerger.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Způsob mapování polí bohatších možností](cognitive-search-output-field-mapping.md)
