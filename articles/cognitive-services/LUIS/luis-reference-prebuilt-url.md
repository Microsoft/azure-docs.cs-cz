---
title: Předem připravené služby LUIS url referenční informace k entitám – Azure | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento článek obsahuje adresu url informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 1615d7e37119f185ebbb3bd674bc006d96d72e4d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030165"
---
# <a name="url-entity"></a>Entita URL
Adresa URL entity extrahuje adresy URL s názvy domény nebo IP adresy. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující adresy URL do aplikace. Adresa URL entity se podporuje v `en-us` pouze jazykovou verzi. 

## <a name="types-of-urls"></a>Typy adres URL
Spravuje se z adresy URL [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) úložiště Github

## <a name="resolution-for-prebuilt-url-entity"></a>Řešení pro předem připravených entit adresy URL
Následující příklad ukazuje rozlišení **builtin.url** entity.

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

Další informace o [ordinální](luis-reference-prebuilt-ordinal.md), [číslo](luis-reference-prebuilt-number.md), a [teploty](luis-reference-prebuilt-temperature.md) entity.