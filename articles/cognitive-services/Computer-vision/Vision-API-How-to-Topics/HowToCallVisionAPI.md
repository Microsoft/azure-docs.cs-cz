---
title: Computer Vision API volat | Dokumentace Microsoftu
description: Zjistěte, jak volat rozhraní API pro počítačové zpracování obrazu pomocí REST ve službě Cognitive Services.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: 34705681e665b57a89c43f31ca695e0acb45ae3f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35760215"
---
# <a name="how-to-call-the-computer-vision-api"></a>Jak volat rozhraní API pro počítačové zpracování obrazu

Tato příručka ukazuje způsob volání REST pomocí rozhraní API pro počítačové zpracování obrazu. Ukázky jsou napsané v jazyce C# pomocí klientské knihovny rozhraní API pro počítačové zpracování obrazu i jako volání HTTP POST nebo GET. Se zaměříme na:

-   Jak získat "Tags", "Popis" a "Kategorie".
-   Jak získat informace "Domain-specific" (celebrit).

### <a name="Prerequisites">Požadavky</a> 
Adresa URL obrázku nebo cesta k místně uložený obrázek.
  * Podporované metody zadávání znaků: Nezpracovaná binární ve formě application/octet-stream adresa URL obrázku nebo image
  * Podporované formáty image: JPEG, PNG, GIF, BMP
  * Velikost souboru obrázku: menší než 4MB
  * Obrázek dimenze: větší než 50 × 50 pixelů
  
V následujících příkladech je ukázán následující funkce:

1. Vráceny analýza bitovou kopii a získávání pole značky a popis.
2. Analýza obrázku s modelem specifického pro doménu (konkrétně model "celebrit to") a získávání odpovídající za následek JSON retune.

Funkce jsou rozdělené na:

  * **Jedna možnost:** obor analýzy – analýza daný model
  * **Možnost 2:** Rozšířená analýza - analýza poskytnout další podrobnosti se [taxonomie kategorií 86](../Category-Taxonomy.md)
  
### <a name="Step1">Krok 1: Povolit volání rozhraní API</a> 
Všechna volání rozhraní API pro počítačové zpracování obrazu vyžadují klíč předplatného. Tento klíč se musí buď předat prostřednictvím parametru řetězce dotazu, nebo zadat v hlavičce požadavku. 

Chcete-li získat klíč předplatného, přečtěte si téma [postup získání klíčů předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md
).

**1.** Předání klíč předplatného pomocí řetězce dotazu, níže jsou uvedeny jako příklad rozhraní API pro počítačové zpracování obrazu:

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

**2.** Předání klíč předplatného je také zadat v hlavičce požadavku protokolu HTTP:

```ocp-apim-subscription-key: <Your subscription key>```

**3.** Při použití klientské knihovny, klíč předplatného předána do konstruktoru VisionServiceClient:

```var visionClient = new VisionServiceClient(“Your subscriptionKey”);```

### <a name="Step2">Krok 2: Nahrání obrázku do rozhraní API pro počítačové zpracování obrazu služby a získat značky, popisy a celebrit</a>
Základní způsob, jak provádět volání rozhraní API pro počítačové zpracování obrazu je tak, že nahrajete image přímo. To se provádí odesláním požadavku "POST" typu obsahu application/octet-stream s data načtená z image. "Tags" a "Popis" Tato metoda nahrávání bude stejný pro všechna volání rozhraní API pro počítačové zpracování obrazu. Jediný rozdíl bude parametry dotazu, který uživatel zadá. 

Tady je postup pro získání "Tags" a "Popis" pro danou image:

**Jedna možnost:** získat seznam "Tags" a jedné "Popis"
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
**Možnost 2** získání seznamu "Tags" pouze, nebo pouze seznam "Popisu":

###### <a name="tags-only"></a>Pouze značky:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Jen pro popis:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```
### <a name="here-is-how-to-get-domain-specific-analysis-in-our-case-for-celebrities"></a>Tady je postup, chcete-li získat analýzy specifického pro doménu (v našem případě pro celebrit).

**Jedna možnost:** obor analýzy – analýza daný model
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```
Pro tuto možnost všechny ostatní parametry dotazu {visualFeatures, podrobnosti} nejsou platné. Pokud chcete zobrazit všechny podporované modely, použijte: 
```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```
**Možnost 2:** Rozšířená analýza - analýza poskytnout další podrobnosti se [taxonomie kategorií 86](../Category-Taxonomy.md)

Pro aplikace, ve které chcete získat analýzy obecný obrázek kromě podrobností z jednoho nebo více doménově specifické modely můžeme rozšířit rozhraní API v1 pomocí parametru dotazu modely.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```
Tato metoda po vyvolání, budeme nazývat třídění 86 kategorie nejprve. Pokud některou z kategorií shodovat s modelem známé/odpovídající, dojde k překontrolovat třídění volání. Například pokud "podrobnosti = all", nebo "details" zahrnout "celebrit", budeme nazývat model celebrit poté, co se nazývá třídění 86 kategorie a výsledek bude obsahovat osoba kategorie. Tím se zvýší latence pro uživatele zajímá celebrit ve srovnání s jednu možnost.

Všechny parametry dotazu v1 se chová stejně v tomto případě.  Pokud visualFeatures = kategorie není zadán, bude implicitně povoleno.

### <a name="Step3">Krok 3: Načtení a pochopení výstup JSON pro analyze & visualFeatures značky, popis =</a>

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
Značky    | objekt    | Objekt nejvyšší úrovně pro pole se značkami
značky []. Jméno | řetězec    | Klíčové slovo z značky třídění
značky []. Skóre    | číslo    | Skóre spolehlivosti mezi 0 a 1.
description  | objekt   | Objekt nejvyšší úrovně pro popis.
[] Description.Tags |    řetězec  | Seznam značek.  Jestliže neexistuje dostatek důvěru v schopnost vytvořit popisek, značky možná pouze informace k dispozici pro volajícího.
Description.Captions[].text | řetězec    | Fráze popisující bitovou kopii.
Description.Captions[].CONFIDENCE   | číslo    | Spolehlivost vyhrazeného frázi.

### <a name="Step4">Krok 4: Načtení a pochopení výstup JSON doménově specifické modely</a>

**Jedna možnost:** obor analýzy – analýza daný model

Výstup bude pole se značkami, například jako v tomto příkladu:
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

**Možnost 2:** Rozšířená analýza - analýza taxonomie kategorií 86 poskytnout další podrobnosti

Pro doménově specifické modely pomocí dvou možnost (rozšířené analýzy) kategorie návratový typ je rozšířit. Příklad:
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

Pole kategorie je seznam jednoho nebo více [86 kategorie](../Category-Taxonomy.md) původní taxonomie. Nezapomeňte, že kategorie s koncovkou podtržítkem bude odpovídat této kategorie a jeho podřízené prvky (například people_ stejně jako people_group pro model celebrit).

Pole   | Typ  | Obsah
------|------|------|
Kategorie | objekt | Objekt nejvyšší úrovně
.name kategorií]    | řetězec   | Název z taxonomie kategorií 86
kategorie .score]  | číslo    | Skóre spolehlivosti mezi 0 a 1
kategorie .detail]  | objekt?      | Objekt volitelných podrobností

Všimněte si, že pokud se shodují více kategorií (například třídění 86 kategorie vrátí skóre pro people_ a people_young Pokud model = celebrit), podrobnosti jsou připojeny k nejobecnější úroveň shody (people_ v tomto příkladu).

### <a name="Errors">Odpovědi na chyby</a>
Tyto jsou stejné jako vision.analyze, k další chybě NotSupportedModel chyba (HTTP 400), které mohou být vráceny v jedna možnost a možnost dva scénáře. Pro možnost 2 (rozšířené analýzy) Pokud nejsou rozpoznány žádné modely zadané v podrobnostech, rozhraní API vrátí NotSupportedModel, i v případě, že jeden nebo více z nich jsou platné.  Uživatelé mohou volat listModels a zjistěte, jaké modely jsou podporována.

### <a name="Summary">Souhrn</a>

Jedná se o základní funkce rozhraní API pro počítačové zpracování obrazu: jak můžete nahrávat obrázky a načíst na oplátku cenné metadat.

Pokud chcete používat rozhraní REST API, přejděte na [Reference k rozhraní API pro zpracování obrazu počítače](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
 
