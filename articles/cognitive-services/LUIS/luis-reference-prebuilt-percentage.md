---
title: Procento předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje procento informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 9b9faaae78cd1e3590291aef68db47f57f050f3d
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165686"
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