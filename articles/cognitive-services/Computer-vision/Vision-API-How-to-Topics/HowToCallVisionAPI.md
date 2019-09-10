---
title: 'Příklad: Volání rozhraní API pro analýzu obrázků – Počítačové zpracování obrazu'
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak použít k volání rozhraní API pro počítačové zpracování obrazu REST v Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 386503a7089c910b52a87cca8d9f2f2203ae0cad
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859069"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Příklad: Postup volání rozhraní API pro počítačové zpracování obrazu

V tomto průvodci si ukážeme, jak použít REST k volání rozhraní API pro počítačové zpracování obrazu. Ukázky jsou napsané v jazyce C# pomocí klientské knihovny rozhraní API pro počítačové zpracování obrazu a také jako volání HTTP s operacemi POST/GET. Na co se zaměříme:

- Jak získat značky, popis a kategorie.
- Jak získat informace o určité doméně (celebrity).

## <a name="prerequisites"></a>Požadavky

- Adresa URL obrázku nebo cesta k místně uloženému obrázku.
- Podporované metody zadávání: Nezpracovaný binární obrázek ve formě datového proudu aplikace/oktetu nebo adresy URL obrázku
- Podporované formáty obrázků: JPEG, PNG, GIF, BMP
- Velikost souboru obrázku: Méně než 4 MB
- Dimenze obrázku: Větší než 50 × 50 pixelů
  
V následujících příkladech si ukážeme tyto funkce:

1. analýza obrazu, získání pole značek a vrácení popisu.
2. Analýza obrazu s použitím modelu určité domény (konkrétně modelu „celebrities“) a získání odpovídajících výsledků ve formátu JSON.

Funkce můžeme rozdělit na:

- **Možnost jedna:** Vymezená analýza – analýza pouze daného modelu
- **Možnost dvě:** Vylepšená analýza – analýza vám poskytne další podrobnosti s [taxonomií 86-Categories](../Category-Taxonomy.md) .
  
## <a name="authorize-the-api-call"></a>Autorizace volání rozhraní API

Ke každému volání rozhraní API pro počítačové zpracování obrazu potřebujete klíč předplatného. Tento klíč je potřeba předat buď jako parametr řetězce dotazu, nebo ho zadat v hlavičce požadavku.

Bezplatný zkušební klíč si můžete [vyzkoušet Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče.

1. Předání klíče předplatného prostřednictvím řetězce dotazu je vidět v následujícím příkladu rozhraní API pro počítačové zpracování obrazu:

    ```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. Klíč předplatného také můžete zadat v hlavičce požadavku HTTP:

    ```ocp-apim-subscription-key: <Your subscription key>```

1. Při použití klientské knihovny je klíč předplatného předán prostřednictvím konstruktoru třídy ComputerVisionClient a oblast je určena ve vlastnosti klienta:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>Nahrání obrázku do služby rozhraní API pro počítačové zpracování obrazu a získání zpětných značek, popisů a celebrit

Základní způsob, jak volat rozhraní API pro počítačové zpracování obrazu, spočívá přímo v nahrání obrázku. Provede se to odesláním požadavku POST s typem obsahu application/octet-stream společně s daty přečtenými z obrázku. V případě značek a popisu bude metoda odeslání stejná pro všechna volání rozhraní API pro počítačové zpracování obrazu. Jediný rozdíl bude v parametrech dotazu, které zadá uživatel. 

Tady je postup, jak získat k danému obrázku značky a popis:

**Možnost jedna:** Získat seznam značek a jednoho popisu

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

**Druhá možnost:** Získání jenom seznamu „Tags“ (Značky) nebo jenom seznamu „Description“ (Popis):

###### <a name="tags-only"></a>Pouze značky:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Pouze popis:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>Získat analýzu specifickou pro doménu (celebrit)

**Možnost jedna:** Vymezená analýza – analýza pouze daného modelu
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

U této možnosti jsou všechny ostatní parametry dotazu (visualFeatures, details) neplatné. K zobrazení všech podporovaných modelů použijte:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**Možnost dvě:** Vylepšená analýza – analýza vám poskytne další podrobnosti s [taxonomií 86-Categories](../Category-Taxonomy.md) .

Pokud chcete kromě podrobností z jednoho nebo několika modelů určitých domén získat i obecnou analýzu obrázku, rozšířili jsme verzi 1 rozhraní API o parametr dotazu do modelů.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

Při volání této metody se napřed volá klasifikátor 86 kategorií. Pokud některé kategorie odpovídají známým/shodným modelům, bude následovat druhé volání klasifikátoru. Například pokud „details=all“ nebo „details“ obsahují „celebrities“, budeme po volání klasifikátoru 86 kategorií volat model celebrit, aby výsledek obsahoval kategorii osob. Tím se u uživatelů, kteří se zajímají o celebrity, zvýší v porovnání s první možností latence.

Všechny parametry dotazu verze 1 se v tomto případě budou chovat stejně.  Pokud není zadaná možnost visualFeatures=categories, bude povolena implicitně.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Načíst a pochopit výstup JSON pro analýzu

Tady je příklad:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Pole | type | Obsah
------|------|------|
Tags  | `object` | Objekt nejvyšší úrovně pro pole značek.
tags[].Name | `string`  | Klíčové slovo z klasifikátoru značek.
tags[].Score    | `number`  | Skóre spolehlivosti z intervalu 0 až 1.
description  | `object` | Objekt nejvyšší úrovně pro popis.
description.tags[] |    `string`    | Seznam značek.  Pokud vytvořený popisek není dostatečně spolehlivý, může mít volající k dispozici jenom značky.
description.captions[].text | `string`  | Výraz, který popisuje obrázek.
description.captions[].confidence   | `number`  | Spolehlivost výrazu.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Načtení a pochopení výstupu JSON pro modely specifické pro doménu

**Možnost jedna:** Vymezená analýza – analýza pouze daného modelu

Výstupem bude pole značek. Tady je příklad:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

**Možnost dvě:** Vylepšená analýza – analýza vám poskytne další podrobnosti s taxonomií 86-Categories.

Při použití druhé možnosti (rozšířená analýza) u modelů určitých domén je rozšířený typ vrácených kategorií. Příklad:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

Pole kategorií představuje seznam jedné nebo několika z [86 kategorií](../Category-Taxonomy.md) původní taxonomie. Všimněte si také, že kategorie končící podtržítkem odpovídají dané kategorii a podřízeným prvkům (příklad: people_ a people_group v modelu celebrities).

Pole   | type  | Obsah
------|------|------|
Kategorie | `object`   | Objekt nejvyšší úrovně
categories[].name    | `string` | Název z taxonomie 86 kategorií
categories[].score  | `number`  | Skóre spolehlivosti z intervalu 0 až 1
categories[].detail  | `object?`      | Volitelné podrobnosti o objektu

Všimněte si, že pokud vyhovuje více kategorií (klasifikátor 86 kategorií například vrátí při použití modelu celebrities skóre pro people_ i people_young), připojí se podrobnosti k nejobecnější shodné značce (v tomto příkladu people_).

## <a name="errors-responses"></a>Odpovědi na chyby

Jsou stejné jako v případě vision.analyze, ale obsahují další chybu NotSupportedModel (HTTP 400), která může být vrácena v obou scénářích (první i druhá možnost). Pokud při druhé možnosti (rozšířená analýza) nejsou rozpoznané žádné zadané modely, vrátí rozhraní API chybu NotSupportedModel i v případě platného modelu nebo více platných modelů.  Pokud uživatelé chtějí zjistit podporované modely, můžou volat listModels.

## <a name="next-steps"></a>Další postup

Pokud chcete použít REST API, přejděte k [referenčním informacím k rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
