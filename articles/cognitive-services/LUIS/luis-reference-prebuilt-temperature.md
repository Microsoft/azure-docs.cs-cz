---
title: Teplota předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje teploty informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 348030d888383c497d80259b279056d8ff892bfe
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165515"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Teplota předem připravených entit pro aplikace LUIS
Teplota extrahuje celou řadu typů teploty. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující teploty do aplikace. Teplota entity se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Typy teploty
Spravuje se z teplota [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) úložiště GitHub

## <a name="resolution-for-prebuilt-temperature-entity"></a>Řešení pro teploty předem připravených entit
Následující příklad ukazuje rozlišení **builtin.temperature** entity.

```json
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Další informace o [procento](luis-reference-prebuilt-percentage.md), [číslo](luis-reference-prebuilt-number.md), a [stáří](luis-reference-prebuilt-age.md) entity. 