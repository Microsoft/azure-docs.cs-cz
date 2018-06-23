---
title: Předem sestavené LEOŠ entity odkazu na pořadí - Azure | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje informace pořadí předem entity znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 2ff9b083e6cabe455baea3ed777dd6cc00b6fbfe
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321964"
---
# <a name="ordinal-entity"></a>Pořadí entity
Řadová číslovka je číselné reprezentace objektu uvnitř sady: `first`, `second`, `third`. Protože tato entita je již vycvičena, není potřeba přidat příklad utterances obsahující pořadí do tříd Intent aplikace. Pořadí entity je podporována v [mnoho jazykové verze](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typy pořadí
Pořadí je spravovat z [rozpoznávání text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) úložiště Github

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Řešení pro předkompilované pořadí entitu
Následující příklad ukazuje řešení **builtin.ordinal** entity.

```JSON
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Další informace o [procento](luis-reference-prebuilt-percentage.md), [phonenumber](luis-reference-prebuilt-phonenumber.md), a [teploty](luis-reference-prebuilt-temperature.md) entity. 