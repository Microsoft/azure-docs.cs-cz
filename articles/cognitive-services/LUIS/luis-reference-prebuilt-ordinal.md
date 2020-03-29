---
title: Ordinální předbudovaná entita – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o předem sestavené entitě v jazyce Porozumění (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bb3bb27db48255f534e873ed4e93ac62f07016af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273446"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Řadová předem připravená entita pro aplikaci LUIS
Číslo číslo je číselná reprezentace objektu uvnitř `first` `second`sady: , , `third`. Vzhledem k tomu, že tato entita je již trénovaný, není nutné přidávat příklad projevy obsahující ordinal záměry aplikace. Ordinální entita je podporována v [mnoha kulturách](luis-reference-prebuilt-entities.md).

## <a name="types-of-ordinal"></a>Typy ordinálních
Ordinal je spravován z úložiště GitHub [s textem pro rozpoznávání](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Rozlišení pro předem sestavenou ordinální entitu

Pro dotaz jsou vráceny následující objekty entity:

`Order the second option`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je `verbose` s parametrem nastaveným na `false`:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/V3-verbose)
Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[Odpověď V2](#tab/V2)

Následující příklad ukazuje rozlišení **entity builtin.ordinal.**

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Seznamte se s [entitami OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [telefonním číslem](luis-reference-prebuilt-phonenumber.md)a [teplotou.](luis-reference-prebuilt-temperature.md)
