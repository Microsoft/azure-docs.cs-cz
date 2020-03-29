---
title: Typ složené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Složená entita se skládá z jiných entit, jako jsou předem vytvořené entity, jednoduché, regulární výraz y a seznam entity. Samostatné entity tvoří celou entitu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71695154"
---
# <a name="composite-entity"></a>Složená entita 

Složená entita se skládá z jiných entit, jako jsou předem vytvořené entity, jednoduché, regulární výraz y a seznam entity. Samostatné entity tvoří celou entitu. 

**Tato entita se hodí, když data:**

* Jsou spolu příbuzní. 
* Se k sobě v kontextu promluvy navzájem vztahují.
* Použijte různé typy entit.
* Je třeba je seskupit a zpracovat klientskou aplikací jako jednotku informací.
* Mít různé uživatelské projevy, které vyžadují strojové učení.

![složená entita](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Příklad JSON

Zvažte složený entitu předem sestavené `number` a `Location::ToLocation` s následující utterance:

`book 2 tickets to cairo`

Všimněte `2`si, že `cairo`, číslo a , ToLocation mají slova mezi nimi, které nejsou součástí žádné entity. Zelené podtržení, které se používá v popiskované utterance na webu [LUIS,](luis-reference-regions.md) označuje složenou entitu.

![Složená entita](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

Složené entity jsou vráceny v `compositeEntities` poli a všechny entity ve `entities` složeném jsou také vráceny v poli:

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```    

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

Toto je JSON, pokud `verbose=false` je nastavena v řetězci dotazu:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Toto je JSON, pokud `verbose=true` je nastavena v řetězci dotazu:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 


|Datový objekt|Název entity|Hodnota|
|--|--|--|
|Předbudovaná entita - číslo|"builtin.number"|"2"|
|Předbudovaná entita - GeographyV2|"Umístění::Tolocation"|"Káhira"|

## <a name="next-steps"></a>Další kroky

V tomto [kurzu](luis-tutorial-composite-entity.md)přidejte **složenou entitu** do svazku extrahovaných dat různých typů do jedné obsahující entity. Sdružováním dat může klientská aplikace snadno extrahovat související data v různých datových typech.
