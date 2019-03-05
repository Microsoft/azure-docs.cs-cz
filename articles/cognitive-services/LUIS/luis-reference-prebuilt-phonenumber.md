---
title: Telefonní číslo předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje informace telefonním čísle předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 2a3e84de6d956e4b45945c77c11f789d4455bfe2
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338786"
---
# <a name="phonenumber-prebuilt-entity-for-a-luis-app"></a>Telefonní číslo předem připravených entit pro aplikace LUIS
`phonenumber` Entity extrahuje širokou škálu telefonní čísla, včetně směrové číslo země. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy do aplikace. `phonenumber` Entity se podporuje v `en-us` pouze jazykovou verzi. 

## <a name="types-of-phonenumber"></a>Typy telefonní číslo
Spravuje se z telefonní číslo [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) úložiště GitHub

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Řešení pro telefonní číslo předem připravených entit
Následující příklad ukazuje rozlišení **builtin.phonenumber** entity.

```json
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
