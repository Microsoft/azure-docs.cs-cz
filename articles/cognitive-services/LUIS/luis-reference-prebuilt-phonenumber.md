---
title: Služba LUIS předem připravených entit telefonní číslo reference – Azure | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento článek obsahuje informace telefonním čísle předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: bacb09b50c4b1d82daa6be1ef4fc79c88269cf7a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035449"
---
# <a name="phonenumber-entity"></a>Entita Phonenumber
`phonenumber` Entity extrahuje širokou škálu telefonní čísla, včetně směrové číslo země. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy do aplikace. `phonenumber` Entity se podporuje v `en-us` pouze jazykovou verzi. 

## <a name="types-of-phonenumber"></a>Typy telefonní číslo
Spravuje se z telefonní číslo [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) úložiště Github

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Řešení pro telefonní číslo předem připravených entit
Následující příklad ukazuje rozlišení **builtin.phonenumber** entity.

```JSON
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>Další postup

Další informace o [procento](luis-reference-prebuilt-percentage.md), [číslo](luis-reference-prebuilt-number.md), a [teploty](luis-reference-prebuilt-temperature.md) entity. 