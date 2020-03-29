---
title: Detekce objektů - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Seznamte se s koncepty souvisejícími s funkcí detekce objektů rozhraní API pro počítačové zpracování obrazu – využití a omezení.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3957e15a09bd7e7ecd814d169451af3241108b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131662"
---
# <a name="detect-common-objects-in-images"></a>Detekce běžných objektů v obrazech

Detekce objektů je podobná [tagování](concept-tagging-images.md), ale rozhraní API vrátí ohraničovací souřadnice (v obrazových bodech) pro každý nalezený objekt. Pokud například obrázek obsahuje psa, kočku a osobu, operace Detekce tyto objekty zobrazí spolu s jejich souřadnicemi v obraze. Tuto funkci můžete použít ke zpracování vztahů mezi objekty v obraze. Umožňuje také určit, zda je v obraze více instancí stejné značky.

Rozhraní Detect API používá značky založené na objektech nebo živých věcech identifikovaných v obrázku. V současné době neexistuje žádný formální vztah mezi značkování taxonomie a taxonomie detekce objektů. Na koncepční úrovni rozhraní DETECT API vyhledá pouze objekty a živé věci, zatímco rozhraní TAG API může také obsahovat kontextové termíny jako "vnitřní", které nelze lokalizovat pomocí ohraničovacích rámečků.

## <a name="object-detection-example"></a>Příklad detekce objektů

Následující odpověď JSON ukazuje, co počítačové vidění vrátí při zjišťování objektů v ukázkovém obrázku.

![Žena, která používá zařízení Microsoft Surface v kuchyni](./Images/windows-kitchen.jpg)

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

Je důležité si uvědomit omezení detekce objektů, abyste se vyhnuli nebo zmírnili účinky falešných negativů (zmeškaných objektů) a omezených podrobností.

* Objekty se obvykle nedetekují, pokud jsou malé (méně než 5 % obrazu).
* Objekty se obvykle nedetekují, pokud jsou uspořádány těsně dohromady (například stoh desek).
* Objekty nejsou rozlišeny podle značek nebo názvů produktů (například různé druhy sodovek na polici v obchodě). Informace o značce však můžete získat z obrázku pomocí funkce [detekce značky.](concept-brand-detection.md)

## <a name="use-the-api"></a>Použití rozhraní API

Funkce detekce objektů je součástí rozhraní Analyzovat rozhraní [API pro obrázky.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Toto rozhraní API můžete volat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. Zahrnout `Objects` do parametru dotazu **visualFeatures.** Potom, když získáte úplnou odpověď JSON, jednoduše analyzovat řetězec `"objects"` pro obsah oddílu.

* [Úvodní příručka: Počítačové zpracování počítače .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Úvodní příručka: Analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)