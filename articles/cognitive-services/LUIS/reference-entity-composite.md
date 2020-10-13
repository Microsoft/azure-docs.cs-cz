---
title: Typ složené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Složená entita je tvořena dalšími entitami, jako jsou předem připravené entity, jednoduché, regulární výrazy a seznam entit. Samostatné entity tvoří celou entitu.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 97bd079034a39f158e3ca438c484929cb6f00363
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542253"
---
# <a name="composite-entity"></a>Složená entita

Složená entita je tvořena dalšími entitami, jako jsou předem připravené entity, jednoduché, regulární výrazy a seznam entit. Samostatné entity tvoří celou entitu.

> [!CAUTION]
> Tato entita je **zastaralá**. Migrujte prosím na [entitu Machine Learning](reference-entity-machine-learned-entity.md).

**Tato entita je vhodná, když jsou data:**

* Jsou vzájemně propojené.
* Se k sobě v kontextu promluvy navzájem vztahují.
* Použijte nejrůznější typy entit.
* Musí být seskupena a zpracována klientskou aplikací jako jednotka informací.
* Máte celou řadu uživatelských projevy, která vyžadují strojové učení.

![složená entita](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Ukázkový kód JSON

Vezměte v úvahu složenou entitu předem sestavenou `number` a `Location::ToLocation` s následujícím utterance:

`book 2 tickets to cairo`

Všimněte si, že `2` číslo a `cairo` ToLocation obsahují slova mezi nimi, která nejsou součástí žádné entity. Zelené podtržení, které se používá v utterance na webu [Luis](luis-reference-regions.md) , označuje složenou entitu.

![Složená entita](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

Složené entity jsou vráceny v `compositeEntities` poli a všechny entity v rámci složeného typu jsou vráceny také v poli `entities` :

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

#### <a name="v3-prediction-endpoint-response"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Toto je kód JSON, pokud `verbose=false` je nastaven v řetězci dotazu:

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

Toto je kód JSON, pokud `verbose=true` je nastaven v řetězci dotazu:

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

Další informace o entitách:

* [Koncepty](luis-concept-entity-types.md)
* [Postup vytvoření](luis-how-to-add-entities.md)
