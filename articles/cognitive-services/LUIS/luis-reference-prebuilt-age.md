---
title: Předem sestavené LEOŠ entity stáří odkaz - Azure | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje stáří předem entity informace znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 59732469cf0d1e55643f3977958ec34a887130d3
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321985"
---
# <a name="age-entity"></a>Stáří entity
Předkompilované stáří entity zaznamená hodnota stáří jak podle čísel a z hlediska dnů, týdnů, měsíců a let. Protože tato entita je již vycvičena, není potřeba přidat příklad utterances obsahující věk na záměry aplikace. Stáří entity je podporována v [mnoho jazykové verze](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Typy stáří
Stáří spravované od [rozpoznávání text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) úložiště Github

## <a name="resolution-for-prebuilt-age-entity"></a>Řešení pro entitu předem stáří
Následující příklad ukazuje řešení **builtin.age** entity.

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

Další informace o [Měna](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [dimenze](luis-reference-prebuilt-dimension.md) entity. 