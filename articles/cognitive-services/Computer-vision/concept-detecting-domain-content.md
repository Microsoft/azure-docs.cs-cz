---
title: Obsah specifický pro doménu – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak zadat doménu kategorizace imagí, která vrátí podrobnější informace o imagi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8d6dc91ae7bb0f6d7a24064749d9295558a7d39c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "68946325"
---
# <a name="detect-domain-specific-content"></a>Rozpoznávání obsahu specifického doménu

Kromě značek a kategorizace na vysoké úrovni Počítačové zpracování obrazu také podporuje další analýzu specifickou pro doménu pomocí modelů, které byly vyučeny na specializované údaje.

Existují dva způsoby, jak použít modely specifické pro doménu: samy o sebe (s vymezenou analýzou) nebo jako vylepšení funkce kategorizace.

### <a name="scoped-analysis"></a>Analýza s vymezeným oborem

Můžete analyzovat image jenom pomocí zvoleného modelu specifického pro doménu voláním [modelů/rozhraní API \<model\> /analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) .

Následuje ukázková odpověď JSON, kterou vrátí rozhraní API **modelů/celebrit/analyzovat** pro danou Image:

![Satya Nadella, usmívající se](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Rozšířená analýza kategorizace

Můžete také použít modely specifické pro doménu k doplnění obecné analýzy obrázků. Provedete to jako součást [vysoké úrovně kategorizace](concept-categorizing-images.md) zadáním modelů specifických pro doménu v parametru *Details* pro volání metody [analyzovat](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) rozhraní API.

V tomto případě se jako první volá klasifikátor taxonomie 86-Category. Pokud má kterákoli ze zjištěných kategorií odpovídající modelem specifického pro doménu, projde se tento model i tato image a přidají se výsledky.

Následující odpověď JSON ukazuje, jak může být analýza specifická pro doménu zahrnutá jako `detail` uzel v širší analýze kategorizace.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>Seznam modelů specifických pro doménu

V současné době Počítačové zpracování obrazu podporuje následující modely specifické pro doménu:

| Název | Popis |
|------|-------------|
| celebrit | Rozpoznávání celebrit, podporované pro obrázky klasifikované v `people_` kategorii |
| památek | Rozpoznávání bodů podporované pro obrázky klasifikované v `outdoor_` `building_` kategoriích nebo |

Volání [modelů](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) rozhraní API vrátí tyto informace spolu s kategoriemi, pro které lze použít každý model:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

Přečtěte si o konceptech pro [kategorizaci imagí](concept-categorizing-images.md).
