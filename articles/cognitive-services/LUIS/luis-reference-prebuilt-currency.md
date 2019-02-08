---
title: CurrentY předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje měny informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: f3d4c8aeabb33e9f0f8b53f63c60c8d6ca702a83
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876670"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Měna předem připravených entit pro aplikace LUIS
Měny předem připravených entit rozpozná měny v mnoha označení a zemí, bez ohledu na jazykovou verzi aplikace LUIS. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující Měna, která se záměry aplikace. Entita měny se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Typy měny
Spravuje se z měny [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) úložiště GitHub

## <a name="resolution-for-currency-entity"></a>Řešení pro entitu měny
Následující příklad ukazuje rozlišení **builtin.currency** entity.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Další informace o [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimenze](luis-reference-prebuilt-dimension.md), a [e-mailu](luis-reference-prebuilt-email.md) entity. 
