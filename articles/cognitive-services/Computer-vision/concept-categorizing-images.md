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
ms.openlocfilehash: 2b9b8da550d80b027da919ba0834e43e2c83d4b4
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945311"
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

## <a name="next-steps"></a>Další kroky

Přečtěte si o konceptech [značek](concept-tagging-images.md) a [popisných obrázků](concept-describing-images.md).
