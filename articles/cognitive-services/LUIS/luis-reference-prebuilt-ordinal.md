---
title: Předem připravené služby LUIS ordinální referenční informace k entitám – Azure | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento článek obsahuje informace o pořadovém místě předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: e80b9dc63accec43532116b81e6eb0b9bd52ee22
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335249"
---
# <a name="ordinal-entity"></a>Entita Ordinal
Řadová číslovka je číselnou reprezentaci objektu uvnitř sady: `first`, `second`, `third`. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující ordinální záměry aplikace. Ordinální entity se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typy pořadí
Spravuje se z pořadí [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) úložiště Github

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Řešení pro předem připravených entit podle pořadového čísla
Následující příklad ukazuje rozlišení **builtin.ordinal** entity.

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