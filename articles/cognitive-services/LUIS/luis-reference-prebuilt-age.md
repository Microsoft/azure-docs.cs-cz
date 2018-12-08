---
title: Stáří předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje stáří informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 2965f9fcedba55536ef25469975679bffe27b4e9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53102652"
---
# <a name="age-entity"></a>Entita Age
Stáří předem připravených entit zachycuje hodnotu stáří jak číselně a co se týče dnů, týdnů, měsíců a roků. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující stáří na záměry aplikace. Stáří entity se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Typy věku
Spravuje se z věku [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) úložiště Github

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