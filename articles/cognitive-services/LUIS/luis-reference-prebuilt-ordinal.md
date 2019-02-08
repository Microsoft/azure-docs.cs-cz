---
title: Ordinální předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje informace o pořadovém místě předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: f6762c74dbe6b16791e597464584b91e696220f1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863446"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Ordinální předem připravených entit pro aplikace LUIS
Řadová číslovka je číselnou reprezentaci objektu uvnitř sady: `first`, `second`, `third`. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující ordinální záměry aplikace. Ordinální entity se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typy pořadí
Spravuje se z pořadí [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) úložiště GitHub

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Řešení pro předem připravených entit podle pořadového čísla
Následující příklad ukazuje rozlišení **builtin.ordinal** entity.

```json
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
