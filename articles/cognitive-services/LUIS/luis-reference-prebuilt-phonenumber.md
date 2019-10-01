---
title: Předem připravené entity telefonního čísla – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje telefonní číslo předem sestavených informací o entitách v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 9e237b09bd2b0ecc3e91e10697f6c025f73057da
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677439"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita pro aplikaci pro LUIS s telefonním číslem
Entita `phonenumber` extrahuje celou řadu telefonních čísel včetně kódu země. Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do aplikace přidat příklad projevy. Entita `phonenumber` je podporována pouze v jazykové verzi `en-us`. 

## <a name="types-of-a-phone-number"></a>Typy telefonního čísla
`Phonenumber` se spravuje z úložiště GitHub pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Řešení pro tuto předem vytvořenou entitu

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

Následující příklad ukazuje řešení entity **Builtin. PhoneNumber** .

```json
{
  "query": "my mobile is 1 (800) 642-7676",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Následující JSON má parametr `verbose` nastavený na `false`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

Následující JSON má parametr `verbose` nastavený na `true`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ],
            "$instance": {
                "phonenumber": [
                    {
                        "type": "builtin.phonenumber",
                        "text": "1 (800) 642-7676",
                        "startIndex": 13,
                        "length": 16,
                        "score": 1,
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

Přečtěte si o [procentech](luis-reference-prebuilt-percentage.md), [číslech](luis-reference-prebuilt-number.md)a [teplotních](luis-reference-prebuilt-temperature.md) entitách. 
