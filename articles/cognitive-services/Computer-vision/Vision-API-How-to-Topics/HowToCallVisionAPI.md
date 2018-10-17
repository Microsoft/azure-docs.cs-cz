---
title: 'Příklad: Volání rozhraní API pro počítačové zpracování obrazu'
titlesuffix: Azure Cognitive Services
description: Přečtěte si, jak použít k volání rozhraní API pro počítačové zpracování obrazu REST v Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: sample
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: e8297fbe59ebe2dea9caf112ebea4517447cf9e0
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981741"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Příklad: Jak volat rozhraní API pro počítačové zpracování obrazu

V tomto průvodci si ukážeme, jak použít REST k volání rozhraní API pro počítačové zpracování obrazu. Ukázky jsou napsané v jazyce C# pomocí klientské knihovny rozhraní API pro počítačové zpracování obrazu a také jako volání HTTP s operacemi POST/GET. Na co se zaměříme:

-   Jak získat značky, popis a kategorie.
-   Jak získat informace o určité doméně (celebrity).

### <a name="Prerequisites">Požadavky</a> 
Adresa URL obrázku nebo cesta k místně uloženému obrázku.
  * Podporované metody vstupů: Nezpracovaný binární obrázek ve tvaru streamu typu application/octet nebo adresa URL obrázku.
  * Podporované formáty obrázku: JPEG, PNG, GIF, BMP.
  * Velikost souboru obrázku: méně než 4 MB.
  * Rozměry obrázku: větší než 50 × 50 pixelů.
  
V následujících příkladech si ukážeme tyto funkce:

1. analýza obrazu, získání pole značek a vrácení popisu.
2. Analýza obrazu s použitím modelu určité domény (konkrétně modelu „celebrities“) a získání odpovídajících výsledků ve formátu JSON.

Funkce můžeme rozdělit na:

  * **První možnost:** zúžená analýza – analýza pouze daného modelu
  * **Druhá možnost:** rozšířená analýza – tato analýza využívá [86 taxonomických kategorií](../Category-Taxonomy.md) a nabízí další podrobnosti
  
### <a name="Step1">1. krok: Autorizace volání rozhraní API</a> 
Ke každému volání rozhraní API pro počítačové zpracování obrazu potřebujete klíč předplatného. Klíč je potřeba předat buď jako parametr řetězce dotazu, nebo ho zadat v hlavičce požadavku. 

Informace o získání klíče předplatného najdete v tématu [Jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md
).

**1.** Předání klíče předplatného prostřednictvím řetězce dotazu je vidět v následujícím příkladu rozhraní API pro počítačové zpracování obrazu:

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

**2.** Klíč předplatného také můžete zadat v hlavičce požadavku HTTP:

```ocp-apim-subscription-key: <Your subscription key>```

**3.** Pokud použijete klientskou knihovnu, předáte klíč předplatného prostřednictvím konstruktoru VisonServiceClient:

```var visionClient = new VisionServiceClient(“Your subscriptionKey”);```

### <a name="Step2">2. krok: Nahrání obrázku do služby rozhraní API pro počítačové zpracování obrazu a vrácení značek, popisů a celebrit</a>
Základní způsob, jak volat rozhraní API pro počítačové zpracování obrazu, spočívá přímo v nahrání obrázku. Provede se to odesláním požadavku POST s typem obsahu application/octet-stream společně s daty přečtenými z obrázku. V případě značek a popisu bude metoda odeslání stejná pro všechna volání rozhraní API pro počítačové zpracování obrazu. Jediný rozdíl bude v parametrech dotazu, které zadá uživatel. 

Tady je postup, jak získat k danému obrázku značky a popis:

**První možnost:** Získání seznamu „Tags“ (Značek) a jedné položky „Description“ (Popis)
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```
```
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```
**Druhá možnost:** Získání jenom seznamu „Tags“ (Značky) nebo jenom seznamu „Description“ (Popis):

###### <a name="tags-only"></a>Jenom značky:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Jenom popis:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```
### <a name="here-is-how-to-get-domain-specific-analysis-in-our-case-for-celebrities"></a>Tady je postup, jak získat analýzu určité domény (v našem případě jsou to celebrity).

**První možnost:** zúžená analýza – analýza pouze daného modelu
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```
U této možnosti jsou všechny ostatní parametry dotazu (visualFeatures, details) neplatné. K zobrazení všech podporovaných modelů použijte: 
```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```
**Druhá možnost:** rozšířená analýza – tato analýza využívá [86 taxonomických kategorií](../Category-Taxonomy.md) a nabízí další podrobnosti

Pokud chcete kromě podrobností z jednoho nebo několika modelů určitých domén získat i obecnou analýzu obrázku, rozšířili jsme verzi 1 rozhraní API o parametr dotazu do modelů.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```
Při volání této metody se napřed volá klasifikátor 86 kategorií. Pokud některé kategorie odpovídají známým/shodným modelům, bude následovat druhé volání klasifikátoru. Například pokud „details=all“ nebo „details“ obsahují „celebrities“, budeme po volání klasifikátoru 86 kategorií volat model celebrit, aby výsledek obsahoval kategorii osob. Tím se u uživatelů, kteří se zajímají o celebrity, zvýší v porovnání s první možností latence.

Všechny parametry dotazu verze 1 se v tomto případě budou chovat stejně.  Pokud není zadaná možnost visualFeatures=categories, bude povolena implicitně.

### <a name="Step3">3. krok: Načtení výstupu JSON a jeho pochopení pro analyze&visualFeatures=Tags, Description</a>

Tady je příklad:
```
  {
    “tags”: [
    {
    "name": "outdoor",
        "score": 0.976
    },
    {
    "name": "bird",
        "score": 0.95
    }
            ],
    “description”: 
    {
    "tags": [
    "outdoor",
    "bird"
            ],
    "captions": [
    {
    "text”: “partridge in a pear tree”,
            “confidence”: 0.96
    }
                ]
    }
  }
```
Pole   | Typ  | Obsah
------|------|------|
Značky    | objekt    | Objekt nejvyšší úrovně pro pole značek.
tags[].Name | řetězec    | Klíčové slovo z klasifikátoru značek.
tags[].Score    | číslo    | Skóre spolehlivosti z intervalu 0 až 1.
description  | objekt   | Objekt nejvyšší úrovně pro popis.
description.tags[] |    řetězec  | Seznam značek.  Pokud vytvořený popisek není dostatečně spolehlivý, může mít volající k dispozici jenom značky.
description.captions[].text | řetězec    | Výraz, který popisuje obrázek.
description.captions[].confidence   | číslo    | Spolehlivost výrazu.

### <a name="Step4">4. krok: Načtení a pochopení výstupu ve formátu JSON z modelů určitých domén</a>

**První možnost:** zúžená analýza – analýza pouze daného modelu

Výstupem bude pole značek. Tady je příklad:
```
  { 
    "result": [ 
      { 
    "name": "golden retriever", 
    "score": 0.98
    },
    { 
    "name": "Labrador retriever", 
    "score": 0.78
    }
              ]
  }
```

**Druhá možnost:** rozšířená analýza – tato analýza využívá 86 taxonomických kategorií a nabízí další podrobnosti

Při použití druhé možnosti (rozšířená analýza) u modelů určitých domén je rozšířený typ vrácených kategorií. Příklad:
```
  {
    "requestId": "87e44580-925a-49c8-b661-d1c54d1b83b5",
    "metadata":     {
      "width": 640,
      "height": 430,
      "format": "Jpeg"
                    },
    "result": {
      "celebrities": 
      [
        {
        "name": "Richard Nixon",
        "faceRectangle": {
          "left": 107,
          "top": 98,
          "width": 165,
          "height": 165
                         },
        "confidence": 0.9999827
        }
      ]
  }
```

Pole kategorií představuje seznam jedné nebo několika z [86 kategorií](../Category-Taxonomy.md) původní taxonomie. Všimněte si také, že kategorie končící podtržítkem odpovídají dané kategorii a podřízeným prvkům (příklad: people_ a people_group v modelu celebrities).

Pole   | Typ  | Obsah
------|------|------|
categories | objekt | Objekt nejvyšší úrovně
categories[].name    | řetězec   | Název z taxonomie 86 kategorií
categories[].score  | číslo    | Skóre spolehlivosti z intervalu 0 až 1
categories[].detail  | objekt?      | Volitelné podrobnosti o objektu

Všimněte si, že pokud vyhovuje více kategorií (klasifikátor 86 kategorií například vrátí při použití modelu celebrities skóre pro people_ i people_young), připojí se podrobnosti k nejobecnější shodné značce (v tomto příkladu people_).

### <a name="Errors">Chybové odpovědi</a>
Jsou stejné jako v případě vision.analyze, ale obsahují další chybu NotSupportedModel (HTTP 400), která může být vrácena v obou scénářích (první i druhá možnost). Pokud při druhé možnosti (rozšířená analýza) nejsou rozpoznané žádné zadané modely, vrátí rozhraní API chybu NotSupportedModel i v případě platného modelu nebo více platných modelů.  Pokud uživatelé chtějí zjistit podporované modely, můžou volat listModels.

### <a name="Summary">Souhrn</a>

Základními funkcemi rozhraní API pro počítačové zpracování obrazu je nahrání obrázků a následné načtení cenných metadat.

Pokud chcete použít REST API, přejděte k [referenčním informacím k rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
 
