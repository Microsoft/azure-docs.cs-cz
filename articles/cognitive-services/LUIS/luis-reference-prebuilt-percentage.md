---
title: Předem připravené služby LUIS procento referenční informace k entitám – Azure | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento článek obsahuje procento informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 6929fc3cc41db4e4bd4a1f8bda62c953a3722eff
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041159"
---
# <a name="percentage-entity"></a>Entita Percentage
Procento čísla můžete uvádět ve zlomcích, `3 1/2`, nebo jako procento, `2%`. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující procento záměry aplikace. Procento entity se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Typy procenta
Procento vedete z [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) úložiště Github

## <a name="resolution-for-prebuilt-percentage-entity"></a>Řešení pro procento předem připravených entit
Následující příklad ukazuje rozlišení **builtin.percentage** entity.

```JSON
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Další informace o [ordinální](luis-reference-prebuilt-ordinal.md), [číslo](luis-reference-prebuilt-number.md), a [teploty](luis-reference-prebuilt-temperature.md) entity. 