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
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 85e3589d7467691e2b9a11879510ab980bbd875a
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273466"
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


LUIS zahrnuje rozpoznanou hodnotu **`builtin.number`** entitu v poli `resolution` odpovědi JSON, kterou vrátí.

## <a name="resolution-for-prebuilt-number"></a>Řešení pro předem připravených číslo

Pro dotaz se vrátí následující objekty entity:

`order two dozen eggs`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je s parametrem `verbose` nastaveným na `false`:

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/V3-verbose)

Následující JSON je s parametrem `verbose` nastaveným na `true`:

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

Následující příklad ukazuje služby luis, odpověď ve formátu JSON, který obsahuje řešení hodnota 24, utterance "z dvou tuctů".

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
