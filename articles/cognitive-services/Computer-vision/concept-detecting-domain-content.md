---
title: Obsah specifický pro doménu - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak určit doménu kategorizace bitových obrázků, abyste vrátili podrobnější informace o obrázku.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68946325"
---
# <a name="detect-domain-specific-content"></a>Rozpoznávání obsahu specifického doménu

Kromě označování a kategorizace na vysoké úrovni podporuje počítačové vidění také další analýzu specifickou pro doménu pomocí modelů, které byly vyškoleny na specializovaných datech.

Existují dva způsoby použití modelů specifických pro doménu: samy o sobě (analýza s vymezeným oborem) nebo jako vylepšení funkce kategorizace.

### <a name="scoped-analysis"></a>Analýza s rozsahem

Obrázek můžete analyzovat pouze pomocí vybraného modelu specifického pro doménu voláním [rozhraní\<MODELS/model\>/Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API.

Následuje ukázka json odpovědi vrácené **modely/celebrity/analyzovat** ROZHRANÍ API pro daný obrázek:

![Satya Nadella stojící, s úsměvem](./images/satya.jpeg)

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

### <a name="enhanced-categorization-analysis"></a>Vylepšená analýza kategorizace

Můžete také použít modely specifické pro doménu k doplnění obecné analýzy obrázků. Provedete to jako součást [kategorizace na vysoké úrovni](concept-categorizing-images.md) zadáním modelů specifických pro doménu v *parametru podrobnosti* [analyzovat](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) volání rozhraní API.

V tomto případě se nejprve nazývá klasifikátor taxonomie 86 kategorie. Pokud některá z detekovaných kategorií má odpovídající model specifický pro doménu, je bitová kopie předána také prostřednictvím tohoto modelu a výsledky jsou přidány.

Následující odpověď JSON ukazuje, jak lze zahrnout `detail` analýzu specifickou pro doménu jako uzel do širší analýzy kategorizace.

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

V současné době program Computer Vision podporuje následující modely specifické pro doménu:

| Name (Název) | Popis |
|------|-------------|
| Celebrity | Rozpoznávání celebrit, podporované pro `people_` obrázky zařazené do kategorie |
| Památek | Rozpoznávání orientačních bodů, podporované `outdoor_` `building_` pro obrázky zařazené do kategorií nebo |

Volání [rozhraní](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API modelů vrátí tyto informace spolu s kategoriemi, na které lze jednotlivé modely použít:

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

Seznamte se s koncepty [kategorizace obrázků](concept-categorizing-images.md).
