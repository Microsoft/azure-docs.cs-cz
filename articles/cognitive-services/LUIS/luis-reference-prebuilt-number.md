---
title: Počet předem sestavených entit – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje počet předem sestavených informací o entitách v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: cb97cc5b0004442e00b970202dd01f76aa971a2a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677579"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Počet předem sestavené entity pro aplikaci LUIS
Existuje mnoho způsobů, jak se číselné hodnoty používají k kvantifikaci, vyjádření a popisu částí informací. Tento článek se zabývá pouze některými možnými příklady. LUIS interpretuje variace v uživatelském projevy a vrátí konzistentní číselné hodnoty. Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující číslo. 

## <a name="types-of-number"></a>Typy čísel
Číslo je spravované z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) v GitHubu.

## <a name="examples-of-number-resolution"></a>Příklady rozlišení čísel

| Promluva        | Entita   | Rozlišení |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS zahrnuje rozpoznanou hodnotu entity **`builtin.number`** v poli `resolution` odpovědi JSON, kterou vrátí.

## <a name="resolution-for-prebuilt-number"></a>Řešení předem připraveného čísla


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

Následující příklad ukazuje odpověď JSON z LUIS, která zahrnuje rozlišení hodnoty 24 pro utterance "2 desítky".

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "subtype": "integer",
        "value": "24"
      }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Následující JSON má parametr `verbose` nastavený na `false`:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ]
        }
    }
}
```

Následující JSON má parametr `verbose` nastavený na `true`:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "two dozen",
                        "startIndex": 6,
                        "length": 9,
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

Přečtěte si o [měně](luis-reference-prebuilt-currency.md), [ordinálním](luis-reference-prebuilt-ordinal.md)a [procentuálním podílu](luis-reference-prebuilt-percentage.md). 
