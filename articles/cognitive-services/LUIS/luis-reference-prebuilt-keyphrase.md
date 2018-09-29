---
title: Předem připravené služby LUIS keyphrase referenční informace k entitám – Azure | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento článek obsahuje keyphrase informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/09/2018
ms.author: diberry
ms.openlocfilehash: 4133b7c7c3fabbe92a3208c567d7b4c6c2c27283
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434394"
---
# <a name="keyphrase-entity"></a>Entita klíčové fráze
keyPhrase extrahuje celou řadu klíčových frází z utterance. Nemusíte přidání projevů příklad obsahující keyPhrase do aplikace. keyPhrase entity se podporuje v [mnoha jazykových verzí](luis-language-support.md#languages-supported) jako součást [rozhraní text analytics](../text-analytics/overview.md) funkce. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Řešení pro keyPhrase předem připravených entit
Následující příklad ukazuje rozlišení **builtin.keyPhrase** entity.

```JSON
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Další informace o [procento](luis-reference-prebuilt-percentage.md), [číslo](luis-reference-prebuilt-number.md), a [stáří](luis-reference-prebuilt-age.md) entity.
