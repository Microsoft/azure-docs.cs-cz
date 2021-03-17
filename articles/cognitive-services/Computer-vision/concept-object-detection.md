---
title: Detekce objektu – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s funkcí detekce objektu rozhraní API pro počítačové zpracování obrazu-Usage a Limits.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4269209017ecc0afa740bc3ed56cbdcbd915201e
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533838"
---
# <a name="detect-common-objects-in-images"></a>Zjistit běžné objekty v obrázcích

Detekce objektu je podobná [označování](concept-tagging-images.md), ale rozhraní API vrací Souřadnice ohraničovacího pole (v pixelech) pro každý nalezený objekt. Pokud například obrázek obsahuje pes, Cat a osobu, operace zjišťování zobrazí tyto objekty spolu s jejich souřadnicemi v obrázku. Tuto funkci můžete použít ke zpracování vztahů mezi objekty v imagi. Také umožňuje určit, zda je v obrázku více instancí stejné značky.

Rozhraní API pro detekci používá značky založené na objektech nebo živých akcích identifikovaných na obrázku. V současné době neexistuje žádný formální vztah mezi taxonomií značek a taxonomií detekce objektu. Na koncepční úrovni detekuje rozhraní API pouze objekty a živé věci, zatímco rozhraní API značek může také zahrnovat kontextové výrazy, jako je "interiér", které nelze lokalizovat pomocí ohraničujících polí.

## <a name="object-detection-example"></a>Příklad detekce objektu

Následující odpověď JSON ukazuje, co Počítačové zpracování obrazu vrátí při detekci objektů v ukázkovém obrázku.

![Žena využívající zařízení Microsoft Surface v kuchyni](./Images/windows-kitchen.jpg)

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

Je důležité poznamenat omezení detekce objektů, abyste se vyhnuli nebo zmírnit dopadům falešně negativních hodnot (nezmeškaných objektů) a omezenými podrobnostmi.

* Obecně se nedetekuje objekty, pokud jsou malé (méně než 5% bitové kopie).
* Obecně se nedetekuje objekty, pokud jsou uspořádány blízko sebe (například zásobník talířů).
* Objekty se neliší podle značky nebo názvů produktů (například různých typů sodasů na police úložiště). Z obrázku ale můžete získat informace o značce pomocí funkce pro [detekci značky](concept-brand-detection.md) .

## <a name="use-the-api"></a>Použití rozhraní API

Funkce detekce objektu je součástí rozhraní API pro [analýzu obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Toto rozhraní API můžete zavolat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. `Objects`Do parametru dotazu **visualFeatures** zahrňte. Až získáte úplnou odpověď ve formátu JSON, stačí analyzovat řetězec pro obsah `"objects"` oddílu.

* [Rychlý Start: Počítačové zpracování obrazu REST API nebo klientské knihovny](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
