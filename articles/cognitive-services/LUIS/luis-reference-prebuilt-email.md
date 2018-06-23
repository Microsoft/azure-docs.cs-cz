---
title: Předem sestavené LEOŠ entity e-mailu odkaz - Azure | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje e-mailu předem entity informace znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 517e92202bdd5bc8d970306b9e24999fa62a8e43
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321866"
---
# <a name="email-entity"></a>E-mailu entity
E-mailu extrakce zahrnuje celou e-mailovou adresu z utterance. Protože tato entita je již vycvičena, není potřeba přidat příklad utterances obsahující e-mailu záměry aplikace. E-mailu entity je podporována v `en-us` pouze jazykovou verzi. 

## <a name="resolution-for-prebuilt-email"></a>Řešení pro předkompilované e-mailu
Následující příklad ukazuje řešení **builtin.email** entity.

```JSON
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Další informace o [číslo](luis-reference-prebuilt-number.md), [pořadí](luis-reference-prebuilt-ordinal.md), a [procento](luis-reference-prebuilt-percentage.md). 