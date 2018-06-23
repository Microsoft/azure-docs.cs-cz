---
title: Odkaz adresu url LEOŠ předem entity - Azure | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje adresu url předem entity informace znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 4eacf564a295a568a3e2c8d2f44ad0af3fbbe258
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321957"
---
# <a name="url-entity"></a>Adresa URL entity
Adresa URL entity extrahuje adresy URL s názvy domény nebo IP adresy. Protože tato entita je již vycvičena, není potřeba přidat příklad utterances obsahující adresy URL pro aplikaci. Adresa URL entity je podporována v `en-us` pouze jazykovou verzi. 

## <a name="types-of-urls"></a>Typy adres URL
Adresa URL je spravovat z [rozpoznávání text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) úložiště Github

## <a name="resolution-for-prebuilt-url-entity"></a>Řešení pro entitu předem adresy URL
Následující příklad ukazuje řešení **builtin.url** entity.

```JSON
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

Další informace o [pořadí](luis-reference-prebuilt-ordinal.md), [číslo](luis-reference-prebuilt-number.md), a [teploty](luis-reference-prebuilt-temperature.md) entity.