---
title: Předem sestavené LEOŠ entity procento odkaz - Azure | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje procento předem entity informace znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 4d502e93f8b6bf3af2da05a499c359faeabb51da
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321908"
---
# <a name="percentage-entity"></a>Procento entity
Může se objevit procento čísla jako zlomků, `3 1/2`, nebo jako procento, `2%`. Protože tato entita je již vycvičena, není potřeba přidat příklad utterances obsahující procento do tříd Intent aplikace. Procento entity je podporována v [mnoho jazykové verze](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Typy procento
Procento je spravovat z [rozpoznávání text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) úložiště Github

## <a name="resolution-for-prebuilt-percentage-entity"></a>Řešení pro entitu předem procento
Následující příklad ukazuje řešení **builtin.percentage** entity.

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

Další informace o [pořadí](luis-reference-prebuilt-ordinal.md), [číslo](luis-reference-prebuilt-number.md), a [teploty](luis-reference-prebuilt-temperature.md) entity. 