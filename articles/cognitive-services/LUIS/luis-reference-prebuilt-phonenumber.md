---
title: Předem sestavené LEOŠ entity telefonní číslo odkaz - Azure | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje telefonní číslo předem entity informace znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 0f72b807b9b0ec110a80d67babb1c45902b8c810
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321880"
---
# <a name="phonenumber-entity"></a>Telefonní číslo entity
`phonenumber` Entity extrahuje celou řadu telefonní čísla, včetně kód země. Protože tato entita je již vycvičena, není potřeba přidat příklad utterances k aplikaci. `phonenumber` Entity je podporována v `en-us` pouze jazykovou verzi. 

## <a name="types-of-phonenumber"></a>Typy telefonní číslo
Telefonní číslo je spravovat z [rozpoznávání text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) úložiště Github

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Řešení pro entitu předem telefonní číslo
Následující příklad ukazuje řešení **builtin.phonenumber** entity.

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