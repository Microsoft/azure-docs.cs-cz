---
title: Počet předem sestavených entit – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o čísle předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 88f36fb6d73e2ec88940e7eb53d982824e194074
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560195"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Počet předem sestavené entity pro aplikaci LUIS
Existuje mnoho způsobů, ve kterých se používají číselných hodnot umožňuje vyčíslit, express a popisují informace. Tento článek se týká jenom některé z možných příkladů. Služba LUIS interpretuje kolísání uživatele projevy a vrátí konzistentní číselné hodnoty. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující číslo k záměry aplikace. 

## <a name="types-of-number"></a>Typy číslo
Číslo je spravované z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) v GitHubu.

## <a name="examples-of-number-resolution"></a>Příklady čísel řešení

| Promluva        | Entita   | Řešení |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


Služba LUIS zahrnuje rozpoznaná hodnota **`builtin.number`** entity v `resolution` pole vrátí odpověď JSON.

## <a name="resolution-for-prebuilt-number"></a>Řešení pro předem připravených číslo


### <a name="api-version-2x"></a>Rozhraní API verze 2. x

Následující příklad ukazuje služby luis, odpověď ve formátu JSON, který obsahuje řešení hodnota 24, utterance "z dvou tuctů".

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

### <a name="preview-api-version-3x"></a>Verze Preview rozhraní API verze 3. x

Následující kód JSON je s `verbose` parametrem nastaveným na: `false`

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

Následující kód JSON je s `verbose` parametrem nastaveným na: `true`

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

## <a name="next-steps"></a>Další postup

Další informace o [měny](luis-reference-prebuilt-currency.md), [ordinální](luis-reference-prebuilt-ordinal.md), a [procento](luis-reference-prebuilt-percentage.md). 
