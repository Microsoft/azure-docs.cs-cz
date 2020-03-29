---
title: Předem sestavené entity dimenze – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o dimenzích předem sestavené entity v jazyce Porozumění (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 3c7bebb38d0331c76eeaaa1d9d6464c568a61b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270638"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Předem připravená entita dimenze pro aplikaci LUIS
Předestavěná dimenze detekuje různé typy dimenzí bez ohledu na jazykovou verzi aplikace LUIS. Vzhledem k tomu, že tato entita je již trénovaný, není nutné přidávat příklad projevy obsahující dimenze záměry aplikace. Entita dimenze je podporována v [mnoha kulturách](luis-reference-prebuilt-entities.md).

## <a name="types-of-dimension"></a>Typy dimenzí

Dimenze se spravuje z úložiště GitHub [s textem pro rozpoznávání.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)

## <a name="resolution-for-dimension-entity"></a>Řešení pro entitu dimenze

Pro dotaz jsou vráceny následující objekty entity:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je `verbose` s parametrem nastaveným na `false`:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/V3-verbose)
Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

Následující příklad ukazuje rozlišení entity **builtin.dimension.**

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Přečtěte si o [e-mailových](luis-reference-prebuilt-email.md), [číselných](luis-reference-prebuilt-number.md)a [řadových](luis-reference-prebuilt-ordinal.md) entitách.
