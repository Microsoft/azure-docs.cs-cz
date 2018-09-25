---
title: Předem připravené služby LUIS měny referenční informace k entitám – Azure | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento článek obsahuje měny informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 89350ad6a49ceef8169d1693dafdedf702992504
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033885"
---
# <a name="currency-entity"></a>Entita Currency
Měny předem připravených entit rozpozná měny v mnoha označení a zemí, bez ohledu na jazykovou verzi aplikace LUIS. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující Měna, která se záměry aplikace. Entita měny se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Typy měny
Spravuje se z měny [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) úložiště Github

## <a name="resolution-for-currency-entity"></a>Řešení pro entitu měny
Následující příklad ukazuje rozlišení **builtin.currency** entity.

```JSON
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