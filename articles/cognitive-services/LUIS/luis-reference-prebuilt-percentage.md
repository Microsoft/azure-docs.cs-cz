---
title: Procento předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje procento informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 3cdf8844ae614e301e1477a039d949b055034ba8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208410"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Procento předem připravených entit pro aplikace LUIS
Procento čísla můžete uvádět ve zlomcích, `3 1/2`, nebo jako procento, `2%`. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující procento záměry aplikace. Procento entity se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Typy procenta
Procento vedete z [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) úložiště GitHub

## <a name="resolution-for-prebuilt-percentage-entity"></a>Řešení pro procento předem připravených entit
Následující příklad ukazuje rozlišení **builtin.percentage** entity.

```json
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
