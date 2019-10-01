---
title: Předem vytvořená entita s měnou – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje předem připravené informace o entitách v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 61be3225f22aca821f8c26522ab37eab0c82bc26
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677718"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita v měně pro aplikaci pro LUIS
Entita předem vytvořená měna detekuje měnu v mnoha denominováncích a zemích nebo oblastech, bez ohledu na jazykovou verzi aplikace LUIS. Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující měnu. Entita měny je podporována v [mnoha jazykových verzích](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Typy měny
Měna se spravuje z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) v GitHubu.

## <a name="resolution-for-currency-entity"></a>Řešení pro entitu Currency

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

Následující příklad ukazuje řešení entity **Builtin. Currency** .

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Následující JSON má parametr `verbose` nastavený na `false`:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ]
        }
    }
}
```

Následující JSON má parametr `verbose` nastavený na `true`:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ],
            "$instance": {
                "money": [
                    {
                        "type": "builtin.currency",
                        "text": "$10.99",
                        "startIndex": 23,
                        "length": 6,
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

Seznamte se s entitami [datetimeV2](luis-reference-prebuilt-datetimev2.md), [Dimension](luis-reference-prebuilt-dimension.md)a [email](luis-reference-prebuilt-email.md) . 
