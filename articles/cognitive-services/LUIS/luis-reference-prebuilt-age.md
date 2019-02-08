---
title: Stáří předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje stáří informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: a6aeffb40092252a4e48a200da640f22a149f089
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867303"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Stáří předem připravených entit pro aplikace LUIS
Stáří předem připravených entit zachycuje hodnotu stáří jak číselně a co se týče dnů, týdnů, měsíců a roků. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující stáří na záměry aplikace. Stáří entity se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Typy věku
Spravuje se z věku [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) úložiště GitHub

## <a name="resolution-for-prebuilt-age-entity"></a>Řešení pro stáří předem připravených entit
Následující příklad ukazuje rozlišení **builtin.age** entity.

```json
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
