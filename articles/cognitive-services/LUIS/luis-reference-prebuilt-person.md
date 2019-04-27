---
title: PersonName předem připravených entit
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje personName informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 7b748c507d5c848cc83a8a0c55cb7b05903bc542
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473123"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>PersonName předem připravených entit pro aplikace LUIS
PersonName předem připravených entit rozpozná jména lidí. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující personName k záměry aplikace. personName entity se podporuje v angličtině a čínština [jazykové verze](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Řešení pro entitu personName
Následující příklad ukazuje rozlišení **builtin.personName** entity.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Další informace o [e-mailu](luis-reference-prebuilt-email.md), [číslo](luis-reference-prebuilt-number.md), a [ordinální](luis-reference-prebuilt-ordinal.md) entity. 
