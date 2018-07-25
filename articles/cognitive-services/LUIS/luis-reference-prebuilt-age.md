---
title: Služba LUIS předem připravených entit stáří reference – Azure | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento článek obsahuje stáří informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: f93acb8bece8c66c3ed7197f1c4530011aec3f29
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237396"
---
# <a name="age-entity"></a>Entita Age
Stáří předem připravených entit zachycuje hodnotu stáří jak číselně a co se týče dnů, týdnů, měsíců a roků. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující stáří na záměry aplikace. Stáří entity se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Typy věku
Spravuje se z věku [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) úložiště Github

## <a name="resolution-for-prebuilt-age-entity"></a>Řešení pro stáří předem připravených entit
Následující příklad ukazuje rozlišení **builtin.age** entity.

```JSON
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Další informace o [měny](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [dimenze](luis-reference-prebuilt-dimension.md) entity. 