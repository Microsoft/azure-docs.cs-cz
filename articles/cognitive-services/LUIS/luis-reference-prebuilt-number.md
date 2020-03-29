---
title: Předestavěná entita číslo – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o předem sestavené entitě číslo v jazyce porozumění (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 85e3589d7467691e2b9a11879510ab980bbd875a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273466"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Číslo předem sestavené entity pro aplikaci LUIS
Existuje mnoho způsobů, jak číselné hodnoty se používají ke kvantifikace, vyjádřit a popsat části informací. Tento článek popisuje pouze některé z možných příkladů. Služba LUIS interpretuje změny v projevech uživatelů a vrací konzistentní číselné hodnoty. Vzhledem k tomu, že tato entita je již trénovaný, není nutné přidávat příklad projevy obsahující číslo záměry aplikace.

## <a name="types-of-number"></a>Typy čísel
Číslo je spravováno z úložiště GitHub [s textem pro rozpoznávání](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml)

## <a name="examples-of-number-resolution"></a>Příklady rozlišení čísel

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


Služba LUIS zahrnuje rozpoznanou hodnotu **`builtin.number`** entity v `resolution` poli odpovědi JSON, kterou vrátí.

## <a name="resolution-for-prebuilt-number"></a>Rozlišení pro předem sestavené číslo

Pro dotaz jsou vráceny následující objekty entity:

`order two dozen eggs`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je `verbose` s parametrem nastaveným na `false`:

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/V3-verbose)

Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json
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
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Odpověď V2](#tab/V2)

Následující příklad ukazuje odpověď JSON z LUIS, která zahrnuje rozlišení hodnoty 24, pro utterance "dva tucty".

```json
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
```
* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Informace o [měně](luis-reference-prebuilt-currency.md), [oslnění](luis-reference-prebuilt-ordinal.md)a [procentu](luis-reference-prebuilt-percentage.md).
