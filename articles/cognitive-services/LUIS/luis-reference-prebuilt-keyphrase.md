---
title: Keyphrase předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje keyphrase informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: a2bb486de0999ad61cf2fdfdfb57dd91d376f597
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206593"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase předem připravených entit pro aplikace LUIS
keyPhrase extrahuje celou řadu klíčových frází z utterance. Nemusíte přidání projevů příklad obsahující keyPhrase do aplikace. keyPhrase entity se podporuje v [mnoha jazykových verzí](luis-language-support.md#languages-supported) jako součást [rozhraní text analytics](../text-analytics/overview.md) funkce. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Řešení pro keyPhrase předem připravených entit
Následující příklad ukazuje rozlišení **builtin.keyPhrase** entity.

```json
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
