---
title: Kategorizace imagí – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s funkcí kategorizace imagí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9721ffa807c9adbeb50839113bc64fd23d8eb13f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96533719"
---
# <a name="categorize-images-by-subject-matter"></a>Kategorizace obrázků podle předmětu

Kromě značek a popisu Počítačové zpracování obrazu vrátí kategorie založené na taxonomii zjištěné v obrázku. Na rozdíl od značek jsou kategorie uspořádány v nadřazené/podřízené Hereditary hierarchii a jich je méně (86, na rozdíl od tisíc značek). Všechny názvy kategorií jsou v angličtině. Kategorizace se dá provádět samostatně nebo spolu s novějším modelem značek.

## <a name="the-86-category-concept"></a>Systém 86 kategorií

Počítačové zpracování obrazu může kategorizovat obrázek v podstatě nebo konkrétně pomocí seznamu kategorií 86 v následujícím diagramu. Celou taxonomii v textovém formátu najdete v článku o [taxonomických kategoriích](category-taxonomy.md).

![Seskupené seznamy všech kategorií v taxonomii kategorie](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Příklady kategorizace obrázků

Následující odpověď JSON ukazuje, co Počítačové zpracování obrazu vrátí při kategorizaci ukázkového obrázku na základě jeho vizuálních funkcí.

![Žena na střeše budovy typu Apartment](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

Následující tabulka ilustruje typickou sadu imagí a kategorii vrácenou Počítačové zpracování obrazu pro každý obrázek.

| Image | Kategorie |
|-------|----------|
| ![Čtyři lidé představují dohromady jako rodinu](./Images/family_photo.png) | people_group |
| ![Puppy se koná v poli travního řízení](./Images/cute_dog.png) | animal_dog |
| ![Osoba, která stojí na horských rockech v slunce](./Images/mountain_vista.png) | outdoor_mountain |
| ![Hromádka rolí chleba v tabulce](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Použití rozhraní API

Funkce kategorizace je součástí rozhraní API pro [analýzu obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Toto rozhraní API můžete zavolat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. `Categories`Do parametru dotazu **visualFeatures** zahrňte. Až získáte úplnou odpověď ve formátu JSON, stačí analyzovat řetězec pro obsah `"categories"` oddílu.

* [Rychlý Start: Počítačové zpracování obrazu REST API nebo klientské knihovny](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Další kroky

Přečtěte si o souvisejících konceptech [značek](concept-tagging-images.md) a [popisů](concept-describing-images.md)obrázků.
