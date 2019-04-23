---
title: Zjišťování objektu - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Další koncepty související s funkcí zjišťování objektu z rozhraní API pro počítačové zpracování obrazu – využití a omezení.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 012ab849c926de332da55361c79c76c5a1311169
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368033"
---
# <a name="detect-common-objects-in-images"></a>Zjištění běžně používané objekty v obrázcích

Detekce objektů je podobný [označování](concept-tagging-images.md), ale rozhraní API Vrátí souřadnice ohraničujícího pole (v pixelech) pro každý objekt se nenašel. Například pokud image obsahuje pes, cat a osoby, rozpoznat operace se zobrazí seznam těchto objektů spolu s jejich souřadnice na obrázku. Tato funkce slouží ke zpracování vztahy mezi objekty v obrázku. Můžete ho taky určit, zda existuje více instancí stejné značky v obraze.

Rozhraní API pro detekci se vztahuje na základě objektů nebo věci živých identifikovat na obrázku značky. Aktuálně neexistuje žádné formální vztah mezi taxonomii značek a taxonomie zjišťování objektu. Na koncepční úroveň rozhraní API pro detekci pouze vyhledá objekty a živých věcí, zatímco rozhraní API pro značku použít také jako "vnitřních" kontextové podmínkami, které nelze lokalizovat s funkcí ohraničování polí.

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

Je důležité si uvědomit omezení objektu zjišťování můžete vyhnout nebo důsledky falešně negativní (chybějící objekty) a omezené podrobnosti.

* Objekty nejsou zjištěny obecně v případě, že jsou malé (méně než 5 % obrázku).
* Objekty nejsou zjištěny obecně, pokud jsou uspořádané pod úzce spolupracují (stoh talířů shora, například).
* Objekty nejsou rozlišené pomocí značky nebo názvy produktů (různé druhy sodovky na polici úložiště, například). Však můžete získat značku informace z bitové kopie pomocí [vytvoření detekce](concept-brand-detection.md) funkce.

## <a name="use-the-api"></a>Použití rozhraní API

Funkce zjišťování objektu je součástí [analyzovat Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) rozhraní API. Můžete volat toto rozhraní API prostřednictvím nativní sadou SDK nebo volání REST. Zahrnout `Objects` v **visualFeatures** parametr dotazu. Potom, když dostanete úplnou odpověď JSON, jednoduše analýzu řetězce pro obsah `"objects"` oddílu.

* [Rychlé zprovoznění: Analýza obrázku (sadu .NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Rychlé zprovoznění: Analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)