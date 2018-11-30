---
title: Kategorizace obrázků – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s kategorizace obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 7062d98d40c15f4e9e873038fc12fc1b104c996d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333475"
---
# <a name="categorizing-images"></a>Kategorizace obrázků

Kromě označování a popisy pro počítačové zpracování obrazu vrací taxonomie podle kategorie definované v předchozích verzích. Tyto kategorie jsou taxonomicky uspořádané jako dědičné hierarchie nadřízených a podřízených prvků. Všechny kategorie jsou v angličtině. Bylo možné samostatně nebo s naší novou označování modely.

## <a name="the-86-category-concept"></a>Systém 86 kategorií

Na základě seznamu konceptů 86 vidět v následujícím diagramu, bitovou kopii lze označit od široké do konkrétní. Celou taxonomii v textovém formátu najdete v článku o [taxonomických kategoriích](category-taxonomy.md).

![seskupené seznam všech kategorií taxonomie kategorií](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Příklady kategorizace obrázků

Následující odpověď JSON ukazuje, co pro počítačové zpracování obrazu vrací při zařazení na obrázku podle jeho vizuální funkce.

![Žena na střeše](./Images/woman_roof.png)

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

Následující tabulka ukazuje typické bitové kopie sady a kategorie vrátil pro počítačové zpracování obrazu pro každé image.

| Image | Kategorie |
|-------|----------|
| ![Rodinná fotka](./Images/family_photo.png) | people_group |
| ![Pejsek](./Images/cute_dog.png) | animal_dog |
| ![Na horách](./Images/mountain_vista.png) | outdoor_mountain |
| ![Vizuální analýza pečiva](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [označování imagí](concept-tagging-images.md) a [imagí s popisem](concept-describing-images.md).