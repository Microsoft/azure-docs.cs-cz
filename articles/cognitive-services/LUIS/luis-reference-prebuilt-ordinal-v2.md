---
title: Ordinální v2 předem sestavená entita – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje pořadí předem sestavených informací o entitách v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 1579c50f2dcf78e350ce014ffb1516cec4ac85b3
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933489"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Předdefinovaná entita pořadí v2 pro aplikaci pro LUIS
Pořadové číslo v2 rozbalí [pořadí](luis-reference-prebuilt-ordinal.md) , aby poskytoval relativní odkazy `next`, například, `last`a `previous`. Tyto nejsou extrahovány pomocí pořadí předem sestavené entity.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Řešení pro předem sestavenou ordinální entitu v2

### <a name="api-version-2x"></a>Rozhraní API verze 2. x

Následující příklad ukazuje řešení entity **Builtin. ordinalV2** .

```json
{
    "query": "what is the second to last choice in the list",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.823669851
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.823669851
        }
    ],
    "entities": [
        {
            "entity": "the second to last",
            "type": "builtin.ordinalV2.relative",
            "startIndex": 8,
            "endIndex": 25,
            "resolution": {
                "offset": "-1",
                "relativeTo": "end"
            }
        }
    ]
}
```

### <a name="preview-api-version-3x"></a>Verze Preview rozhraní API verze 3. x

Následující kód JSON je s `verbose` parametrem nastaveným na: `false`

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ]
        }
    }
}
```

Následující kód JSON je s `verbose` parametrem nastaveným na: `true`

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ],
            "$instance": {
                "ordinalV2": [
                    {
                        "type": "builtin.ordinalV2.relative",
                        "text": "the second to last",
                        "startIndex": 8,
                        "length": 18,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

Přečtěte si [](luis-reference-prebuilt-percentage.md)o procentech, [telefonním číslu](luis-reference-prebuilt-phonenumber.md)a o [teplotních](luis-reference-prebuilt-temperature.md) entitách. 
