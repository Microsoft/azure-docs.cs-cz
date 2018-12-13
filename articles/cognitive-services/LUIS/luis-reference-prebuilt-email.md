---
title: Služba LUIS předem připravených entit e-mailem odkaz – Azure | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento článek obsahuje e-mailu předem připravených entit informace v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 1cb12bdc362955da907fb5a5ed64c2a1a43fdc32
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140859"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>E-mailu předem připravených entit pro aplikace LUIS
Extrakce e-mailu obsahuje celé e-mailovou adresu z utterance. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující e-mailu záměry aplikace. E-mailu entity se podporuje v `en-us` pouze jazykovou verzi. 

## <a name="resolution-for-prebuilt-email"></a>Řešení pro předem připravených e-mailu
Následující příklad ukazuje rozlišení **builtin.email** entity.

```json
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