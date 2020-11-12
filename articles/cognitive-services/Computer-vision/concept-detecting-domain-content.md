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
ms.openlocfilehash: 5cd872d66088e165bfc8356ab6d96a0a6135a0e0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538304"
---
# <a name="detect-domain-specific-content"></a>Rozpoznávání obsahu specifického doménu

Kromě značek a kategorizace na vysoké úrovni Počítačové zpracování obrazu také podporuje další analýzu specifickou pro doménu pomocí modelů, které byly vyučeny na specializované údaje.

Existují dva způsoby, jak použít modely specifické pro doménu: samy o sebe (s vymezenou analýzou) nebo jako vylepšení funkce kategorizace.

### <a name="scoped-analysis"></a>Analýza s vymezeným oborem

Můžete analyzovat image jenom pomocí zvoleného modelu specifického pro doménu voláním [modelů/rozhraní API \<model\> /analyze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) .

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

Můžete také použít modely specifické pro doménu k doplnění obecné analýzy obrázků. Provedete to jako součást [vysoké úrovně kategorizace](concept-categorizing-images.md) zadáním modelů specifických pro doménu v parametru *Details* pro volání metody [analyzovat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) rozhraní API.

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

Volání [modelů](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20e) rozhraní API vrátí tyto informace spolu s kategoriemi, pro které lze použít každý model:

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
