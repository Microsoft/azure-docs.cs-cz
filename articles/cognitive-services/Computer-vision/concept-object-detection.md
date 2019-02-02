---
title: Zjišťování objektu - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s zjišťování objektu pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 93ce86a438fca47100a34da2524515b46bcad574
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567158"
---
# <a name="object-detection"></a>Detekce objektů

Detekce objektů je podobný [označování](concept-tagging-images.md), ale rozhraní API Vrátí souřadnice ohraničujícího pole (v pixelech) pro každý objekt se nenašel. Například pokud image obsahuje pes, cat a osoby, rozpoznat operace se zobrazí seznam těchto objektů spolu s jejich souřadnice na obrázku. Tato funkce slouží ke zpracování vztahy mezi objekty v obrázku. Můžete ho taky určit, pokud existuje více instancí stejné značky v obraze.

Rozhraní API pro detekci se vztahuje na základě objektů nebo věci živých identifikovat na obrázku značky. Všimněte si, že v tomto okamžiku, není žádný formální vztah mezi taxonomii pro označování a taxonomie použitých pro zjištění objektu. Na koncepční úroveň rozhraní API pro detekci pouze vyhledá objekty a živých věcí, zatímco rozhraní API pro značku použít také jako "vnitřních" kontextové podmínkami, které nelze lokalizovat s funkcí ohraničování polí.

## <a name="object-detection-example"></a>Příklad zjištění objektu

Následující odpověď JSON ukazuje, co pro počítačové zpracování obrazu vrací při zjišťování objektů v na obrázku.

![Žena kuchyně pomocí zařízení Microsoft Surface](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Omezení

Je důležité si uvědomit omezení pro funkci zjišťování objektu můžete vyhnout nebo důsledky falešně negativní (chybějící objekty) a omezené podrobnosti.
* Objekty nejsou zjištěny obecně v případě, že jsou velmi malé (méně než 5 % obrázku).
* Objekty nejsou zjištěny obecně, pokud jsou velmi úzce spolupracují uspořádané (stoh talířů shora, například).
* Objekty nejsou rozlišené pomocí značky nebo názvy produktů (různé druhy sodovky na polici úložiště, například). Však můžete získat značku informace z bitové kopie pomocí [vytvoření detekce](concept-brand-detection.md) funkce.

## <a name="use-the-api"></a>Použití rozhraní API
Funkce zjišťování objektu je součástí [analyzovat Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) rozhraní API. Můžete volat toto rozhraní API prostřednictvím nativní sadou SDK nebo volání REST. Když získáte úplnou odpověď JSON, jednoduše analyzovat řetězec pro obsah `"objects"` oddílu.

* [Rychlé zprovoznění: Analýza obrázku (sadu .NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Rychlé zprovoznění: Analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)