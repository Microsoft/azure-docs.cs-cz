---
title: Kategorizace obrázků – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Další koncepty související s funkcí kategorizace obrázků z rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f182110d150583ee1c241c23a2e1924d9f3e3bd4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158039"
---
# <a name="image-categorization-with-computer-vision"></a>Kategorizace obrázků s pro počítačové zpracování obrazu

Kromě označování a popisy pro počítačové zpracování obrazu vrací taxonomie podle kategorie definované v předchozích verzích. Tyto kategorie jsou taxonomicky uspořádané jako dědičné hierarchie nadřízených a podřízených prvků. Všechny kategorie jsou v angličtině. Bylo možné samostatně nebo s naší novou označování modely.

## <a name="the-86-category-concept"></a>Systém 86 kategorií

Na základě seznamu konceptů 86 vidět v následujícím diagramu, bitovou kopii lze označit od široké do konkrétní. Celou taxonomii v textovém formátu najdete v článku o [taxonomických kategoriích](category-taxonomy.md).

![seskupené seznam všech kategorií taxonomie kategorií](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Příklady kategorizace obrázků

Následující odpověď JSON ukazuje, co pro počítačové zpracování obrazu vrací při zařazení na obrázku podle jeho vizuální funkce.

![Žena stropu budovy objektu apartment](./Images/woman_roof.png)

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
| ![Čtyřmi lidmi kompenzaci společně jako skupina](./Images/family_photo.png) | people_group |
| ![Štěněte chladniček na grassy pole](./Images/cute_dog.png) | animal_dog |
| ![Osoba na rock Horská oblast, abyste se při západu slunce](./Images/mountain_vista.png) | outdoor_mountain |
| ![Celé řady bread role na tabulku](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [označování imagí](concept-tagging-images.md) a [imagí s popisem](concept-describing-images.md).
