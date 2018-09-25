---
title: PersonName předem připravených entit – odkaz na LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje personName informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 0bc9d59fa44125429bfcb4e6ca9e68d93b81c04d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950569"
---
# <a name="personname-entity"></a>PersonName entity
PersonName předem připravených entit rozpozná jména lidí. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující personName k záměry aplikace. personName entity se podporuje v angličtině a čínština [jazykové verze](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Řešení pro entitu personName
Následující příklad ukazuje rozlišení **builtin.personName** entity.

```JSON
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