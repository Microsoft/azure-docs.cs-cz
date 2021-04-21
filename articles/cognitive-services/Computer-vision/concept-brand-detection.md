---
title: Detekce značky – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Tento článek popisuje specializovaný režim detekce objektů; rozpoznání značky nebo loga pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 5892fc0bbbd07690ff010e8e1212a914733cbb18
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778980"
---
# <a name="detect-popular-brands-in-images"></a>Detekce oblíbených značek na obrázcích

Rozpoznávání značek je specializovaný režim [detekce objektů](concept-object-detection.md) , který používá databázi tisíců globálních log k identifikaci komerčních značek na obrázcích nebo videu. Tuto funkci můžete použít například k tomu, abyste zjistili, které značky jsou nejoblíbenější na sociálních médiích nebo ve většině druhů v mediálním umístění.

Služba Počítačové zpracování obrazu zjišťuje, zda v dané imagi existují logo značky; Pokud ano, vrátí název značky, hodnocení spolehlivosti a souřadnice ohraničujícího rámečku kolem loga.

Integrovaná databáze loga zahrnuje oblíbené značky v zákaznické elektronikě, ošacení a dalších. Pokud zjistíte, že služba Počítačové zpracování obrazu nedetekuje značku, kterou hledáte, je možné, že bude lepší pracovat s vytvářením a školením vlastního detektoru loga pomocí služby [Custom Vision](../custom-vision-service/index.yml) .

## <a name="brand-detection-example"></a>Příklad detekce značky

Následující odpovědi JSON znázorňují, co Počítačové zpracování obrazu vrací při detekci značek v ukázkových obrázcích.

![Červená košile s popiskem a logem Microsoftu](./Images/red-shirt-logo.jpg)

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

V některých případech značka detektoru zachová jak obrázek loga, tak název stylizované značky jako dvě samostatná loga.

![Šedý Sweatshirt s popiskem a logem Microsoftu](./Images/gray-shirt-logo.jpg)

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

Funkce detekce značky je součástí rozhraní API pro [analýzu obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) . Toto rozhraní API můžete zavolat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. `Brands`Do parametru dotazu **visualFeatures** zahrňte. Až získáte úplnou odpověď ve formátu JSON, stačí analyzovat řetězec pro obsah `"brands"` oddílu.

* [Rychlý Start: Počítačové zpracování obrazu REST API nebo klientské knihovny](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)