---
title: Typ složené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Složená entita je tvořena dalšími entitami, jako jsou předem připravené entity, jednoduché, regulární výrazy a seznam entit. Samostatné entity tvoří celou entitu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695154"
---
# <a name="composite-entity"></a>Složená entita 

Složená entita je tvořena dalšími entitami, jako jsou předem připravené entity, jednoduché, regulární výrazy a seznam entit. Samostatné entity tvoří celou entitu. 

**Tato entita je vhodná, když jsou data:**

* Jsou vzájemně propojené. 
* Jsou vzájemně propojené v kontextu utterance.
* Použijte nejrůznější typy entit.
* Musí být seskupena a zpracována klientskou aplikací jako jednotka informací.
* Máte celou řadu uživatelských projevy, která vyžadují strojové učení.

![složená entita](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Ukázkový kód JSON

Vezměte v úvahu složenou entitu předem sestavených `number` a `Location::ToLocation` s následujícím utterance:

`book 2 tickets to cairo`

Všimněte si, že `2`, číslo a `cairo` ToLocation obsahují slova, která nejsou součástí žádné z entit. Zelené podtržení, které se používá v utterance na webu [Luis](luis-reference-regions.md) , označuje složenou entitu.

![Složená entita](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

Složené entity jsou vráceny v poli `compositeEntities` a všechny entity v rámci složeného pole jsou vráceny také v poli `entities`:

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Toto je JSON, pokud je v řetězci dotazu nastavená hodnota `verbose=false`:

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

Toto je JSON, pokud je v řetězci dotazu nastavená hodnota `verbose=true`:

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
|Předem sestavené číslo entity|"Builtin. Number"|odst|
|Předem sestavená entita – GeographyV2|"Umístění:: ToLocation"|Cairo|

## <a name="next-steps"></a>Další kroky

V tomto [kurzu](luis-tutorial-composite-entity.md)přidejte **složenou entitu** , která bude seskupit extrahovaná data různých typů do jedné obsahující entity. Díky sdružování dat může klientská aplikace snadno extrahovat související data v různých datových typech.
