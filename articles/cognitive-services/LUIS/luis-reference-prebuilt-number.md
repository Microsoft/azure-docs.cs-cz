---
title: Předem sestavené LEOŠ entity číslo odkaz - Azure | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje informace o čísle předem entity znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: aa0b389a0694a3b742259fd42bed08055fbbadbe
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321859"
---
# <a name="number-entity"></a>Počet entit
Existuje mnoho způsobů, ve kterých se používají číselné hodnoty k vyčíslení express a popisují informace. Tento článek se týká jenom některé z možných příkladů. LEOŠ interpretuje rozdíly v utterances uživatele a vrátí konzistentní číselné hodnoty. Protože tato entita je již vycvičena, není potřeba přidat příklad utterances obsahující číslo záměry aplikace. 

## <a name="types-of-number"></a>Typy číslo
Číslo je spravovat z [rozpoznávání text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) úložiště Github

## <a name="examples-of-number-resolution"></a>Příklady číslo řešení

| utterance        | Entita   | Řešení |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LEOŠ zahrnuje známá hodnota **`builtin.number`** entity v `resolution` pole JSON odpovědi vrátí.

## <a name="resolution-for-prebuilt-number"></a>Řešení pro předkompilované číslo
Následující příklad ukazuje odpověď JSON z LEOŠ, překlad hodnota 24, utterance "dvacet", který obsahuje.

```JSON
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

Další informace o [Měna](luis-reference-prebuilt-currency.md), [pořadí](luis-reference-prebuilt-ordinal.md), a [procento](luis-reference-prebuilt-percentage.md). 