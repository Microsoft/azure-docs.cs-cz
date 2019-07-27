---
title: Keyphrase předem vytvořená entita – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje keyphrase informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d7ecb579305cb17525e220bc16be430c77d25080
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563427"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase předem vytvořenou entitu pro aplikaci LUIS
keyPhrase extrahuje celou řadu klíčových frází z utterance. Nemusíte přidání projevů příklad obsahující keyPhrase do aplikace. keyPhrase entity se podporuje v [mnoha jazykových verzí](luis-language-support.md#languages-supported) jako součást [rozhraní text analytics](../text-analytics/overview.md) funkce. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Řešení pro keyPhrase předem připravených entit

### <a name="api-version-2x"></a>Rozhraní API verze 2. x

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
