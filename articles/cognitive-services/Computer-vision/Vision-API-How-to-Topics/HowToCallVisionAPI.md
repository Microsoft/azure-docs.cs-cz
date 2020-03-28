---
title: Volání rozhraní API pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak volat rozhraní API pro počítačové zpracování pomocí rozhraní REST API ve službě Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 298228eedb73298f00654f4f72c201d9ed671090
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72177052"
---
# <a name="call-the-computer-vision-api"></a>Volání rozhraní API pro počítačové zpracování obrazu

Tento článek ukazuje, jak volat rozhraní API pro počítačové zpracování obrazu pomocí rozhraní REST API. Ukázky jsou zapsány v C# pomocí klientské knihovny rozhraní API počítačového zpracování obrazu a jako volání HTTP POST nebo GET. Článek se zaměřuje na:

- Získání značek, popisu a kategorií
- Získání informací specifických pro doménu nebo "celebrity"

## <a name="prerequisites"></a>Požadavky

- Adresa URL obrázku nebo cesta k místně uloženému obrázku
- Podporované metody zadávání vstupů: nezpracovaný binární obraz ve formě aplikace/oktetového proudu nebo adresa URL obrázku
- Podporované formáty obrazových souborů: JPEG, PNG, GIF a BMP
- Velikost souboru obrázku: 4 MB nebo méně
- Rozměry obrazu: &times; 50 50 pixelů nebo více
  
Příklady v tomto článku ukazují následující funkce:

* Analýza obrázku pro vrácení pole značek a popisu
* Analýza obrázku s modelem specifickým pro doménu (konkrétně modelem "celebrity") pro vrácení odpovídajícího výsledku v JSON

Funkce nabízejí následující možnosti:

- **Možnost 1**: Analýza oboru - Analýza pouze určeného modelu
- **Možnost 2**: Vylepšená analýza – analýza za účelem poskytnutí dalších podrobností pomocí [taxonomie 86 kategorií](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Autorizace volání rozhraní API

Ke každému volání rozhraní API pro počítačové zpracování obrazu potřebujete klíč předplatného. Tento klíč musí být předán parametrem řetězce dotazu nebo zadán v hlavičce požadavku.

Chcete-li získat bezplatný zkušební klíč, proveďte některou z následujících akcí:
* Přejděte na stránku [Vyzkoušet kognitivní služby.](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) 
* Chcete-li se přihlásit k odběru služby Computer Vision, přejděte na stránku [Vytvořit účet služby Cognitive Services.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)

Klíč předplatného můžete předat některou z následujících akcí:

* Předajte jej prostřednictvím řetězce dotazu, jako v tomto příkladu rozhraní API pro počítačové zpracování obrazu:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Zadejte ji v hlavičce požadavku HTTP:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Při použití klientské knihovny předavte klíč prostřednictvím konstruktoru ComputerVisionClient a zadejte oblast ve vlastnosti klienta:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Nahrání obrázku do služby Api pro počítačové zpracování obrazu

Základním způsobem, jak provést volání rozhraní API pro počítačové zpracování obrazu, je nahrání obrázku přímo do značek, popisu a celebrit. To provést odesláním požadavku "POST" s binární bitové kopie v těle HTTP spolu s daty číst z obrázku. Metoda upload je stejná pro všechna volání rozhraní API pro počítačové zpracování obrazu. Jediným rozdílem jsou zadané parametry dotazu. 

U zadaného obrázku získáte značky a popis pomocí jedné z následujících možností:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Možnost 1: Získání seznamu značek a popisu

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
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

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Možnost 2: Získejte pouze seznam značek nebo pouze popis

Pouze pro značky spusťte:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Pouze pro popis spusťte:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Získejte analýzu specifickou pro doménu (celebrity)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Možnost 1: Analýza oboru - Analýza pouze určeného modelu
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

U této možnosti jsou všechny ostatní parametry dotazu (visualFeatures, details) neplatné. K zobrazení všech podporovaných modelů použijte:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Možnost 2: Vylepšená analýza – analýza za účelem poskytnutí dalších podrobností pomocí taxonomie 86 kategorií

Pro aplikace, kde chcete získat obecnou analýzu obrázků kromě podrobností z jednoho nebo více modelů specifických pro doménu, rozšiřte rozhraní API v1 pomocí parametru dotazu modely.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Při vyvolání této metody nejprve voláte klasifikátor [86 kategorie.](../Category-Taxonomy.md) Pokud některá z kategorií odpovídá kategorii známého nebo odpovídajícího modelu, dojde k druhému průchodu vyvolání třídění. Pokud například "details=all" nebo "details" obsahuje "celebrity", zavoláte modelu celebrit po volání třídění kategorie 86. Výsledek zahrnuje osobu kategorie. Na rozdíl od možnosti 1 tato metoda zvyšuje latenci pro uživatele, kteří mají zájem o celebrity.

V tomto případě všechny parametry dotazu v1 chovat stejným způsobem. Pokud nezadáte visualFeatures=categories, je implicitně povolena.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Načtení a pochopení výstupu JSON pro analýzu

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

Pole | Typ | Obsah
------|------|------|
Značky  | `object` | Objekt nejvyšší úrovně pro pole tagů.
tags[].Name | `string`  | Klíčové slovo z klasifikátoru značek.
tags[].Score    | `number`  | Skóre důvěry, mezi 0 a 1.
description  | `object` | Objekt nejvyšší úrovně pro popis.
description.tags[] |    `string`    | Seznam značek.  Pokud není dostatečná důvěra ve schopnost vytvářet titulek, značky mohou být pouze informace, které jsou k dispozici volajícímu.
description.captions[].text | `string`  | Výraz, který popisuje obrázek.
description.captions[].confidence   | `number`  | Skóre důvěry pro frázi.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Načtení a pochopení výstupu JSON modelů specifických pro doménu

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Možnost 1: Analýza oboru - Analýza pouze určeného modelu

Výstup je pole značek, jak je znázorněno v následujícím příkladu:

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

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Možnost 2: Vylepšená analýza – analýza za účelem poskytnutí dalších podrobností pomocí taxonomie "86 kategorií"

U modelů specifických pro doménu, které používají možnost 2 (rozšířená analýza), je návratový typ kategorií rozšířen, jak je znázorněno v následujícím příkladu:

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

Pole kategorie je seznam emitovaných kategorií jednou nebo více z [86 kategorií](../Category-Taxonomy.md) v původní taxonomii. Kategorie, které končí podtržítkem, odpovídají této kategorii a jejím podřízeným položkám (například "people_" nebo "people_group" pro model celebrit).

Pole   | Typ  | Obsah
------|------|------|
categories | `object`   | Objekt nejvyšší úrovně.
categories[].name    | `string` | Název ze seznamu taxonomií 86 kategorií.
categories[].score  | `number`  | Skóre důvěry, mezi 0 a 1.
categories[].detail  | `object?`      | (Nepovinné) Objekt podrobností.

Pokud více kategorií odpovídá (například klasifikátor 86 kategorií vrátí skóre pro "people_" a "people_young", když model = celebrity), podrobnosti jsou připojeny k nejobecnější úrovni shody ("people_", v tomto příkladu).

## <a name="error-responses"></a>Chybové odpovědi

Tyto chyby jsou shodné s těmi v vision.analyze, s další chybou NotSupportedModel (HTTP 400), která může být vrácena ve scénářích možnost 1 a možnost 2. Pro možnost 2 (vylepšená analýza), pokud některý z modelů, které jsou zadány v podrobnostech není rozpoznán, rozhraní API vrátí NotSupportedModel, i v případě, že jeden nebo více z nich jsou platné. Chcete-li zjistit, jaké modely jsou podporovány, můžete volat listModels.

## <a name="next-steps"></a>Další kroky

Pokud chcete použít REST API, přejděte k [referenčním informacím k rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
