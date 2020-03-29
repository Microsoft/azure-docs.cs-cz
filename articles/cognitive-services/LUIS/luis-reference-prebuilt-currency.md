---
title: Předvytvořená entita měny – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o předem sestavené entitě měny v jazyce Porozumění (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 56d264fa976967ae4772f50b4aa6f58b30355d6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270768"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Měna předem sestavená entita pro aplikaci LUIS
Předem vytvořená měnová entita detekuje měnu v mnoha denominacích a zemích nebo oblastech bez ohledu na jazykovou verzi aplikace LUIS. Vzhledem k tomu, že tato entita je již trénovaný, není nutné přidat příklad projevy obsahující měnu záměry aplikace. Měnová entita je podporována v [mnoha kulturách](luis-reference-prebuilt-entities.md).

## <a name="types-of-currency"></a>Typy měn
Měna se spravuje z úložiště GitHub [s textem pro rozpoznávání](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Řešení pro měnovou entitu

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je `verbose` s parametrem nastaveným na `false`:

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/V3-verbose)
Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[Odpověď V2](#tab/V2)

Následující příklad ukazuje rozlišení entity **builtin.currency.**

```json
"entities": [
    {
        "entity": "$10.99",
        "type": "builtin.currency",
        "startIndex": 23,
        "endIndex": 28,
        "resolution": {
        "unit": "Dollar",
        "value": "10.99"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Další informace o [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimenze](luis-reference-prebuilt-dimension.md)a [e-mailové](luis-reference-prebuilt-email.md) entity.
