---
title: Adresa URL předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje adresu url informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 3e5f6edf213838345c21598213b4fc2065e66335
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884133"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Adresa URL předem připravených entit pro aplikace LUIS
Adresa URL entity extrahuje adresy URL s názvy domény nebo IP adresy. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující adresy URL do aplikace. Adresa URL entity se podporuje v `en-us` pouze jazykovou verzi. 

## <a name="types-of-urls"></a>Typy adres URL
Spravuje se z adresy URL [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) úložiště GitHub

## <a name="resolution-for-prebuilt-url-entity"></a>Řešení pro předem připravených entit adresy URL
Následující příklad ukazuje rozlišení **builtin.url** entity.

```json
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Další informace o [ordinální](luis-reference-prebuilt-ordinal.md), [číslo](luis-reference-prebuilt-number.md), a [teploty](luis-reference-prebuilt-temperature.md) entity.
