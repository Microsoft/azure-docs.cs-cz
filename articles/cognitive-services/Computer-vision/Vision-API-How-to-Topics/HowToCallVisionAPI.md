---
title: Volání rozhraní API pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Naučte se volat rozhraní API pro počítačové zpracování obrazu pomocí REST API ve službě Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3a9ef3fb009cfb91b20ac7492be193286e2f0410
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432532"
---
# <a name="call-the-computer-vision-api"></a>Volání rozhraní API pro počítačové zpracování obrazu

Tento článek ukazuje, jak volat rozhraní API pro počítačové zpracování obrazu pomocí REST API. Ukázky jsou napsány v jazyce C# pomocí klientské knihovny rozhraní API pro počítačové zpracování obrazu a jako volání HTTP POST nebo GET. Článek se zaměřuje na:

- Získání značek, popisu a kategorií
- Získání informací specifických pro doménu nebo "celebrit"

Příklady v tomto článku ukazují následující funkce:

* Analýza obrázku pro vrácení pole značek a popisu
* Analýza obrázku pomocí modelu specifického pro doménu (konkrétně modelu "celebrit"), který vrátí odpovídající výsledek ve formátu JSON

Tyto funkce nabízí následující možnosti:

- **Možnost 1**: analýza vymezená v oboru – analýza pouze zadaného modelu
- **Možnost 2**: Vylepšená analýza – proveďte analýzu a poskytněte další podrobnosti pomocí [taxonomie 86-Categories](../Category-Taxonomy.md) .

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Počítačové zpracování obrazu službě budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Adresa URL obrázku nebo cesta k místně uložené imagi
* Podporované metody zadávání: nezpracovaný binární obrázek ve formě aplikace, oktetového datového proudu nebo adresy URL obrázku
* Podporované formáty souborů obrázků: JPEG, PNG, GIF a BMP
* Velikost souboru obrázku: 4 MB nebo méně
* Rozměry obrázku: 50 &times; 50 pixelů nebo větší
  
## <a name="authorize-the-api-call"></a>Autorizace volání rozhraní API

Ke každému volání rozhraní API pro počítačové zpracování obrazu potřebujete klíč předplatného. Tento klíč musí být buď předán parametrem řetězce dotazu, nebo zadán v hlavičce požadavku.

Klíč předplatného můžete předat některým z následujících způsobů:

* Předejte ho pomocí řetězce dotazu, jako v tomto příkladu rozhraní API pro počítačové zpracování obrazu:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Zadejte v hlavičce požadavku HTTP:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Při použití klientské knihovny předejte klíč prostřednictvím konstruktoru třídy ComputerVisionClient a určete oblast ve vlastnosti klienta:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Nahrajte obrázek do služby rozhraní API pro počítačové zpracování obrazu.

Základní způsob, jak provést rozhraní API pro počítačové zpracování obrazu volání, je nahrání obrázku přímo do návratových značek, popisu a celebrit. Provedete to tak, že v těle HTTP odešlete žádost "POST" s binárním obrázkem spolu s daty čtenými z image. Metoda upload je stejná pro všechna rozhraní API pro počítačové zpracování obrazu volání. Jediným rozdílem jsou parametry dotazu, které zadáte. 

Pro zadanou bitovou kopii získáte pomocí kterékoli z následujících možností značky a popis:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Možnost 1: získání seznamu značek a popisu

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

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Možnost 2: získání seznamu pouze značek nebo pouze popisu

Pouze pro značky spusťte:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Pouze v případě popisu spusťte:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Získat analýzu specifickou pro doménu (celebrit)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Možnost 1: analýza vymezená v oboru – analýza pouze zadaného modelu
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

U této možnosti jsou všechny ostatní parametry dotazu (visualFeatures, details) neplatné. K zobrazení všech podporovaných modelů použijte:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Možnost 2: Vylepšená analýza – proveďte analýzu a poskytněte další podrobnosti pomocí taxonomie 86-Categories.

U aplikací, u kterých chcete získat obecnou analýzu obrázků kromě podrobností z jednoho nebo více modelů specifických pro doménu, můžete rozhraní v1 API rozšířit pomocí parametru dotazu modely.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Při vyvolání této metody nejprve zavolejte klasifikátor [kategorie 86](../Category-Taxonomy.md) . Pokud některá z kategorií odpovídá známému nebo odpovídajícímu modelu, dojde k druhému volání třídění. Pokud například "Details = All" nebo "Details" obsahuje "celebrit", zavoláte model celebrit po volání klasifikátoru kategorie 86. Výsledek zahrnuje kategorii osoba. Na rozdíl od možnosti 1 Tato metoda zvyšuje latenci pro uživatele, kteří mají zájem o celebrit.

V tomto případě se všechny parametry dotazu v1 chovají stejným způsobem. Pokud nezadáte visualFeatures = categories, je implicitně povolená.

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

Pole | Typ | Content
------|------|------|
Značky  | `object` | Objekt nejvyšší úrovně pro pole značek.
tags[].Name | `string`    | Klíčové slovo z třídění značek.
tags[].Score    | `number`    | Hodnocení spolehlivosti mezi 0 a 1.
description     | `object`    | Objekt nejvyšší úrovně pro popis.
description.tags[] |    `string`    | Seznam značek.  Pokud není dostatečná důvěra v možnosti vytvoření titulku, mohou být značky jedinou informací, které jsou k dispozici volajícímu.
description.captions[].text    | `string`    | Výraz, který popisuje obrázek.
description.captions[].confidence    | `number`    | Skóre spolehlivosti fráze.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Načtení a pochopení výstupu JSON pro modely specifické pro doménu

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Možnost 1: analýza vymezená v oboru – analýza pouze zadaného modelu

Výstupem je pole značek, jak je znázorněno v následujícím příkladu:

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

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Možnost 2: Vylepšená analýza – proveďte analýzu a poskytněte další podrobnosti pomocí taxonomie "86-Categories".

U modelů specifických pro doménu pomocí možnosti 2 (vylepšená analýza) je typ vrácené kategorie rozšířený, jak je znázorněno v následujícím příkladu:

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

Pole kategorie je seznam jednoho nebo více [kategorií 86](../Category-Taxonomy.md) v původní taxonomii. Kategorie, které končí podtržítkem, odpovídají této kategorii a jejím dětem (například "people_" nebo "people_group" pro model celebrit).

Pole    | Typ    | Content
------|------|------|
categories | `object`    | Objekt nejvyšší úrovně.
categories[].name     | `string`    | Název ze seznamu taxonomie 86-Category.
categories[].score    | `number`    | Hodnocení spolehlivosti mezi 0 a 1.
categories[].detail     | `object?`      | Volitelné Objekt podrobností.

Pokud se shoduje více kategorií (například klasifikátor 86 kategorie vrací skóre pro jak "people_", tak "people_young", "při modelu = celebrit) jsou podrobnosti v tomto příkladu připojeny k nejobecnější shodě úrovně (" people_, ").

## <a name="error-responses"></a>Chybové odpovědi

Tyto chyby jsou stejné jako v Vision. Analyzujte s další chybou NotSupportedModel (HTTP 400), která se může vrátit ve scénářích možnosti 1 a možnost 2. V případě možnosti 2 (rozšířená analýza), pokud některý z modelů, které jsou zadány v podrobnostech, rozhraní API vrátí NotSupportedModel, i když je jeden nebo více z nich platný. Chcete-li zjistit, jaké modely jsou podporovány, můžete volat listModels.

## <a name="next-steps"></a>Další kroky

Pokud chcete použít REST API, přejděte k [referenčním informacím k rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b).
