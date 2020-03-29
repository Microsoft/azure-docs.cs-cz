---
title: Detekce značky - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Tento článek popisuje specializovaný režim detekce objektů; značky a/nebo loga pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 50e4fe1e2573c8566bbdf5697bb81b025a00935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131742"
---
# <a name="detect-popular-brands-in-images"></a>Detekce oblíbených značek na obrázcích

Detekce značek je specializovaný způsob [detekce objektů,](concept-object-detection.md) který používá databázi tisíců globálních log k identifikaci komerčních značek v obrázcích nebo videu. Pomocí této funkce můžete například zjistit, které značky jsou nejoblíbenější na sociálních médiích nebo nejrozšířenější v umístění mediálního produktu.

Služba Počítačové vidění zjistí, zda jsou loga značky v daném obrázku; Pokud ano, vrátí název značky, skóre spolehlivosti a souřadnice ohraničovacího rámečku kolem loga.

Vestavěná databáze log pokrývá oblíbené značky v oblasti spotřební elektroniky, oblečení a dalších. Pokud zjistíte, že značka, kterou hledáte, není službou Počítačové vidění detekována, může vám být lépe obsluhováno vytváření a školení vlastního detektoru loga pomocí služby [Custom Vision.](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)

## <a name="brand-detection-example"></a>Příklad detekce značky

Následující odpovědi JSON ilustrují, co počítačové vidění vrátí při detekci značek v ukázkových obrázcích.

![Červená košile s označením Microsoft a logem](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

V některých případech detektor značky vyzvedne jak obrázek loga, tak stylizovanou značku jako dvě samostatná loga.

![Šedá mikina se štítkem a logem společnosti Microsoft](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Použití rozhraní API

Funkce detekce značky je součástí rozhraní Analyzovat rozhraní API [pro obrázky.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Toto rozhraní API můžete volat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. Zahrnout `Brands` do parametru dotazu **visualFeatures.** Potom, když získáte úplnou odpověď JSON, jednoduše analyzovat řetězec `"brands"` pro obsah oddílu.

* [Úvodní příručka: Počítačové zpracování počítače .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Úvodní příručka: Analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)
