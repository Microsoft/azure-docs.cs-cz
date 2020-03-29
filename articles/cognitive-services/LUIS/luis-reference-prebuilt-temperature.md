---
title: Předdefinovaná entita teploty – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o entitě s předdefinovanými teplotami v jazyce Luis (Language Understanding).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 7e2b48c6353f56ab2269a8718146cb765797adba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270367"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Předdefinovaná entita teploty pro aplikaci LUIS
Teplota extrahuje různé typy teplot. Vzhledem k tomu, že tato entita je již trénovaný, není nutné přidat příklad projevy obsahující teplotu do aplikace. Teplotní entita je podporována v [mnoha kulturách](luis-reference-prebuilt-entities.md).

## <a name="types-of-temperature"></a>Typy teplot
Teplota je spravována z úložiště GitHub [s textem pro rozpoznávání](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Rozlišení pro předem připravenou teplotní entitu

Pro dotaz jsou vráceny následující objekty entity:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je `verbose` s parametrem nastaveným na `false`:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/V3-verbose)
Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
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

Následující příklad ukazuje rozlišení entity **builtin.temperature.**

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Informace o [procentech](luis-reference-prebuilt-percentage.md), [číslech](luis-reference-prebuilt-number.md)a [věkových](luis-reference-prebuilt-age.md) entitách.
