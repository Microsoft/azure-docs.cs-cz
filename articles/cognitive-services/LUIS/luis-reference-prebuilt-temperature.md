---
title: Předem připravené služby LUIS teploty referenční informace k entitám – Azure | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento článek obsahuje teploty informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 39d33a3132a400ce0f1853062968cfdaf81c693f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041468"
---
# <a name="temperature-entity"></a>Entita Temperature
Teplota extrahuje celou řadu typů teploty. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující teploty do aplikace. Teplota entity se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Typy teploty
Spravuje se z teplota [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) úložiště Github

## <a name="resolution-for-prebuilt-temperature-entity"></a>Řešení pro teploty předem připravených entit
Následující příklad ukazuje rozlišení **builtin.temperature** entity.

```JSON
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