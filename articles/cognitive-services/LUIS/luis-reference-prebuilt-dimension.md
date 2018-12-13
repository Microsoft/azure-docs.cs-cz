---
title: Dimenze předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje dimenze informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 775c7800c2ea9abe4b960a2e82cebb85e3c74d3b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53098760"
---
# <a name="dimension-entity"></a>Entita Dimension
Různé typy dimenzí, bez ohledu na jazykovou verzi aplikace LUIS rozpozná dimenze předem připravených entit. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující rozměry záměry aplikace. Dimenze entity se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Typy dimenze

Spravuje se z dimenze [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) úložiště Github


## <a name="resolution-for-dimension-entity"></a>Řešení pro entity dimenze
Následující příklad ukazuje rozlišení **builtin.dimension** entity.

```json
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Další informace o [e-mailu](luis-reference-prebuilt-email.md), [číslo](luis-reference-prebuilt-number.md), a [ordinální](luis-reference-prebuilt-ordinal.md) entity. 