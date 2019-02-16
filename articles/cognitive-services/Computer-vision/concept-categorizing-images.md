---
title: Kategorizace obrázků – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Další koncepty související s funkcí kategorizace obrázků z rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a85fb65ad971bfc70bdab0689e99921e616b56ac
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308855"
---
# <a name="categorize-images"></a>Kategorizace obrázků

Kromě značek a popis pro počítačové zpracování obrazu vrací na základě taxonomie kategorií v obrázku. Na rozdíl od značky kategorie jsou uspořádány do hierarchie dědičného nadřazené a podřízené a jsou méně z nich (na rozdíl od tisíce značky 86). Všechny názvy kategorií jsou v angličtině. Kategorizace to provést, samostatně nebo společně s modelem novější značky.

## <a name="the-86-category-concept"></a>Systém 86 kategorií

Počítačové zpracování obrazu můžete zařadit do kategorií bitovou kopii široce nebo konkrétně, pomocí seznamu 86 categroies v následujícím diagramu. Celou taxonomii v textovém formátu najdete v článku o [taxonomických kategoriích](category-taxonomy.md).

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
