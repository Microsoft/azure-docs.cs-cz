---
title: Předem vytvořená entita stáří – LUIS
titleSuffix: Azure Cognitive Services
description: V tomto článku najdete informace o předem připravené entitě v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 1b2ff6b64661010136d43b8d1f10abb58a88102a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677733"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Předem připravená entita pro aplikaci pro LUIS
Předem vytvořená věková entita zachycuje věkovou hodnotu číselně i ve dnech, týdnech, měsících a rocích. Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující stáří. Věková entita je podporována v [mnoha jazykových verzích](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Typy stáří
Stáří je spravované z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) v GitHubu.

## <a name="resolution-for-prebuilt-age-entity"></a>Řešení pro předem sestavenou entitu Age

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

Následující příklad ukazuje řešení entity **Builtin. Age** .

```json
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Následující JSON má parametr `verbose` nastavený na `false`:

```json
{
    "query": "A 90 day old utilities bill is quite late.",
    "prediction": {
        "normalizedQuery": "a 90 day old utilities bill is quite late.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.558252
            }
        },
        "entities": {
            "age": [
                {
                    "number": 90,
                    "unit": "Day"
                }
            ]
        }
    }
}
```

Následující JSON má parametr `verbose` nastavený na `true`:

```json
{
    "query": "A 90 day old utilities bill is quite late.",
    "prediction": {
        "normalizedQuery": "a 90 day old utilities bill is quite late.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.558252
            }
        },
        "entities": {
            "age": [
                {
                    "number": 90,
                    "unit": "Day"
                }
            ],
            "$instance": {
                "age": [
                    {
                        "type": "builtin.age",
                        "text": "90 day old",
                        "startIndex": 2,
                        "length": 10,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

* * * 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Seznamte se s entitami [Currency](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)a [Dimension](luis-reference-prebuilt-dimension.md) . 
