---
title: Předem sestavené LEOŠ entity dimenze reference - Azure | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje dimenze předem entity informace znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 3b2758f1d68ae3659f2e43cad555d327b21f8732
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321978"
---
# <a name="dimension-entity"></a>Dimenze entity
Předkompilované dimenze entity zjistí různé typy dimenzí, bez ohledu na jazyková verze LEOŠ aplikace. Protože tato entita je již vycvičena, není potřeba přidat příklad utterances obsahující dimenze záměry aplikace. Dimenze entity je podporována v [mnoho jazykové verze](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Typy dimenze

Dimenze je spravovat z [rozpoznávání text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) úložiště Github


## <a name="resolution-for-dimension-entity"></a>Řešení pro dimenze entity
Následující příklad ukazuje řešení **builtin.dimension** entity.

```JSON
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

Další informace o [e-mailu](luis-reference-prebuilt-email.md), [číslo](luis-reference-prebuilt-number.md), a [pořadí](luis-reference-prebuilt-ordinal.md) entity. 