---
title: Zjištění obsahu specifického pro doménu – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak zadat doménu kategorizace obrázků vrací podrobné informace o obrázku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 66137f01672820584f97273ddca26a66ada781ba
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312518"
---
# <a name="detect-domain-specific-content"></a>Zjištění obsahu specifického pro doménu

Kromě toho kategorizace značek a vysoké úrovně, pro počítačové zpracování obrazu podporuje také další specifického pro doménu analýza s využitím modelů, které jste se školení na specializované data. 

Existují dva způsoby, jak použít doménově specifické modely: samostatně (s vymezeným oborem analýzy) nebo jako neboli podmínky vylepšují funkci kategorizaci.

### <a name="scoped-analysis"></a>S vymezeným oborem analýzy

Můžete analyzovat image pomocí zvolený model specifického pro doménu voláním [modely /\<modelu\>/Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) rozhraní API. 

Tady je ukázka JSON odpovědi vrácené **modely/celebrit/analyzovat** rozhraní API pro danou image:

![Satya Nadella Stálého](./images/satya.jpeg)

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

### <a name="enhanced-categorization-analysis"></a>Rozšířená kategorizace analýzy  

Doménově specifické modely můžete použít také k doplnění analýzu obecné obrázku. Můžete to provést v rámci [vysoké úrovně kategorizace](concept-categorizing-images.md) zadáním doménově specifické modely v *podrobnosti* parametr [analyzovat](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) volání rozhraní API. 

V takovém případě třídění taxonomie 86 kategorií jako první. Pokud zjištěné kategorií modelu odpovídající specifického pro doménu, image se předává přidaná i modelu a výsledky. 

Následující odpověď JSON ukazuje, jak specifického pro doménu analýzy může být zahrnut jako `detail` uzel v širší kategorizaci analýzy.

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

## <a name="list-the-domain-specific-models"></a>Seznam doménově specifické modely

Počítačové zpracování obrazu v současné době podporuje následující doménově specifické modely:

| Název | Popis |
|------|-------------|
| celebrit | Rozpoznávání celebrit, podporované pro klasifikaci obrázků v `people_` kategorie |
| Zajímavá | Rozpoznávání památek, podporované pro klasifikaci obrázků v `outdoor_` nebo `building_` kategorie |

Volání [modely](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) rozhraní API vrátí tyto informace spolu s kategorií, na které můžete použít každý model:

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

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [kategorizace obrázků](concept-categorizing-images.md).
