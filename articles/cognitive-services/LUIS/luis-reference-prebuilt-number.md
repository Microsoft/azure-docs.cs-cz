---
title: Počet předem sestavených entit – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje počet předem sestavených informací o entitách v Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 13594886b83d4474ee2531185db5868a5198ca64
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541950"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Počet předem sestavené entity pro aplikaci LUIS
Existuje mnoho způsobů, jak se číselné hodnoty používají k kvantifikaci, vyjádření a popisu částí informací. Tento článek se zabývá pouze některými možnými příklady. LUIS interpretuje variace v uživatelském projevy a vrátí konzistentní číselné hodnoty. Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující číslo.

## <a name="types-of-number"></a>Typy čísel
Číslo je spravované z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) v GitHubu.

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


LUIS obsahuje rozpoznanou hodnotu **`builtin.number`** entity v `resolution` poli odpovědi JSON, kterou vrátí.

## <a name="resolution-for-prebuilt-number"></a>Řešení předem připraveného čísla

Pro dotaz se vrátí následující objekty entity:

`order two dozen eggs`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující kód JSON je s `verbose` parametrem nastaveným na `false` :

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/V3-verbose)

Následující kód JSON je s `verbose` parametrem nastaveným na `true` :

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
#### <a name="v2-response"></a>[Odpověď v2](#tab/V2)

Následující příklad ukazuje odpověď JSON z LUIS, která zahrnuje rozlišení hodnoty 24 pro utterance "2 desítky".

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

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si o [měně](luis-reference-prebuilt-currency.md), [ordinálním](luis-reference-prebuilt-ordinal.md)a [procentuálním podílu](luis-reference-prebuilt-percentage.md).
