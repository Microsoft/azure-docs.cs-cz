---
title: Procento předem sestavené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje procentuální údaje předem připravené entity v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 0005d679819f7516e1ded6294030cd42813e085f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677522"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Procentuální předem sestavená entita pro aplikaci pro LUIS
Procentuální čísla se můžou vyskytovat jako zlomky, `3 1/2` nebo jako procento, `2%`. Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující procento. Procentuální hodnota entity je podporována v [mnoha jazykových verzích](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Typy procent
Procento je spravované z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) v GitHubu.

## <a name="resolution-for-prebuilt-percentage-entity"></a>Řešení pro předem sestavenou entitu procent

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

Následující příklad ukazuje řešení entity **Builtin. PERCENTAGE** .

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Následující JSON má parametr `verbose` nastavený na `false`:

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ]
        }
    }
}
```

Následující JSON má parametr `verbose` nastavený na `true`:

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ],
            "$instance": {
                "percentage": [
                    {
                        "type": "builtin.percentage",
                        "text": "2%",
                        "startIndex": 36,
                        "length": 2,
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

Přečtěte si o [pořadí](luis-reference-prebuilt-ordinal.md), [čísle](luis-reference-prebuilt-number.md)a [teplotních](luis-reference-prebuilt-temperature.md) entitách. 
