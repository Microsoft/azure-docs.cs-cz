---
title: Číslo předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje informace o čísle předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 12b5daec94b894f0fcd171963737d99ca670b77d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225933"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Číslo předem připravených entit pro aplikace LUIS
Existuje mnoho způsobů, ve kterých se používají číselných hodnot umožňuje vyčíslit, express a popisují informace. Tento článek se týká jenom některé z možných příkladů. Služba LUIS interpretuje kolísání uživatele projevy a vrátí konzistentní číselné hodnoty. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující číslo k záměry aplikace. 

## <a name="types-of-number"></a>Typy číslo
Spravuje se z číslo [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) úložiště GitHub

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
        "value": "24"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Další informace o [měny](luis-reference-prebuilt-currency.md), [ordinální](luis-reference-prebuilt-ordinal.md), a [procento](luis-reference-prebuilt-percentage.md). 
