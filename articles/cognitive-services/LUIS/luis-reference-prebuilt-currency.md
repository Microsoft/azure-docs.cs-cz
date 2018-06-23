---
title: Předem sestavené LEOŠ entity Měna odkaz - Azure | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje měna předem entity informace znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 3e20642f6734b0247d23db1a63317eb8b4a96b5e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321845"
---
# <a name="currency-entity"></a>Měna entity
Předkompilované měna entity zjistí měny v mnoha názvy a zemích, bez ohledu na jazykové LEOŠ aplikace. Protože tato entita je již vycvičena, není potřeba přidat příklad utterances obsahující měny do tříd Intent aplikace. Měna entity je podporována v [mnoho jazykové verze](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Typy měny
Je Currency spravovaných [rozpoznávání text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) úložiště Github

## <a name="resolution-for-currency-entity"></a>Řešení pro entitu měny
Následující příklad ukazuje řešení **builtin.currency** entity.

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