---
title: Služba LUIS předem připravených entit e-mailem odkaz – Azure | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento článek obsahuje e-mailu předem připravených entit informace v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 6d05f62ad725a89b0a34b21b8a8d36bb8fa464b1
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441007"
---
# <a name="email-entity"></a>Entita Email
Extrakce e-mailu obsahuje celé e-mailovou adresu z utterance. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující e-mailu záměry aplikace. E-mailu entity se podporuje v `en-us` pouze jazykovou verzi. 

## <a name="resolution-for-prebuilt-email"></a>Řešení pro předem připravených e-mailu
Následující příklad ukazuje rozlišení **builtin.email** entity.

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

Další informace o [číslo](luis-reference-prebuilt-number.md), [ordinální](luis-reference-prebuilt-ordinal.md), a [procento](luis-reference-prebuilt-percentage.md). 