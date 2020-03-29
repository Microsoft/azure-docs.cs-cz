---
title: Kategorizace obrazu - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Seznamte se s koncepty souvisejícími s funkcí kategorizace obrázků rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 477349f1addf71a30e8ecb179266d8eac5510887
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244746"
---
# <a name="categorize-images-by-subject-matter"></a>Kategorizace obrázků podle předmětu

Kromě značek a popisu vrátí počítačové vidění kategorie založené na taxonomii zjištěné v bitové kopii. Na rozdíl od značek jsou kategorie uspořádány v dědičné hierarchii nadřazeného/podřízeného a je jich méně (86, na rozdíl od tisíců značek). Všechny názvy kategorií jsou v angličtině. Kategorizaci lze provést samostatně nebo vedle novějších značek modelu.

## <a name="the-86-category-concept"></a>Systém 86 kategorií

Počítačové vidění může kategorizovat obrázek široce nebo konkrétně pomocí seznamu 86 kategorií v následujícím diagramu. Celou taxonomii v textovém formátu najdete v článku o [taxonomických kategoriích](category-taxonomy.md).

![Seskupené seznamy všech kategorií v kategorii taxonomie](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Příklady kategorizace obrázků

Následující odpověď JSON ukazuje, co počítačové vidění vrátí při kategorizaci ukázkového obrázku na základě jeho vizuálních funkcí.

![Žena na střeše bytového domu](./Images/woman_roof.png)

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

Následující tabulka znázorňuje typickou sadu obrázků a kategorii vrácenou programem Počítačové vidění pro každý obrázek.

| Image | Kategorie |
|-------|----------|
| ![Čtyři lidé se společně vydávali za rodinu](./Images/family_photo.png) | people_group |
| ![Štěně sedící na travnatém poli](./Images/cute_dog.png) | animal_dog |
| ![Osoba stojící na horské skále při západu slunce](./Images/mountain_vista.png) | outdoor_mountain |
| ![Hromada chleba rolí na stole](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Použití rozhraní API

Funkce kategorizace je součástí rozhraní Analyzovat rozhraní API [pro obrázky.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Toto rozhraní API můžete volat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. Zahrnout `Categories` do parametru dotazu **visualFeatures.** Potom, když získáte úplnou odpověď JSON, jednoduše analyzovat řetězec `"categories"` pro obsah oddílu.

* [Úvodní příručka: Počítačové zpracování počítače .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Úvodní příručka: Analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Další kroky

Naučte se související pojmy [označování obrázků](concept-tagging-images.md) a [popisu obrázků](concept-describing-images.md).
