---
title: Kategorizace obrázků – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s kategorizace obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 345432cbc8a552ba7f6a4902cec72bcc1bf9feac
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983544"
---
# <a name="categorizing-images"></a>Kategorizace obrázků

Kromě označování a popisy pro počítačové zpracování obrazu vrací taxonomie podle kategorie definované v předchozích verzích. Tyto kategorie jsou uspořádané jako taxonomie s dědičného hierarchie nadřazený/podřízený. Všechny kategorie jsou v angličtině. Bylo možné samostatně nebo s naší novou označování modely.

## <a name="the-86-category-concept"></a>Koncept 86 kategorie

Na základě seznamu konceptů 86 vidět v následujícím diagramu, bitovou kopii lze označit od široké do konkrétní. Úplné taxonomie v textovém formátu, naleznete v tématu [taxonomie kategorií](category-taxonomy.md).

![Analýza kategorie](./Images/analyze_categories.png)

## <a name="image-categorization-examples"></a>Příklady kategorizace obrázků

Následující odpověď JSON ukazuje, co pro počítačové zpracování obrazu vrací při zařazení na obrázku podle jeho vizuální funkce.

![Žena stříška](./Images/woman_roof.png)

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
| ![Rodinných fotografií](./Images/family_photo.png) | people_group |
| ![Roztomilá pes](./Images/cute_dog.png) | animal_dog |
| ![Venkovní Horská oblast](./Images/mountain_vista.png) | outdoor_mountain |
| ![Pro zpracování obrazu analyzovat Bread potravin](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [označování imagí](concept-tagging-images.md) a [imagí s popisem](concept-describing-images.md).