---
title: Procento předem sestavené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje procento předem předem informace o entitě v porozumění jazykům (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 31ea1c36139abcb1e102161ad76a203073ba4dfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270502"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Procento předem sestavené entity pro aplikaci LUIS
Procenta čísla se mohou `3 1/2`zobrazit jako zlomky , nebo jako procento, `2%`. Vzhledem k tomu, že tato entita je již trénovaný, není nutné přidávat příklad projevy obsahující procento záměry aplikace. Procentuální entita je podporována v [mnoha kulturách](luis-reference-prebuilt-entities.md).

## <a name="types-of-percentage"></a>Typy procent
Procento se spravuje z úložiště GitHub [s textem pro rozpoznávání](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Rozlišení pro předem připravenou procentuální entitu

Pro dotaz jsou vráceny následující objekty entity:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je `verbose` s parametrem nastaveným na `false`:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/V3-verbose)
Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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

Následující příklad ukazuje rozlišení **entity builtin.percentage.**

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Seznamte se s [entitami osla](luis-reference-prebuilt-ordinal.md), [počtu](luis-reference-prebuilt-number.md)a [teplotě.](luis-reference-prebuilt-temperature.md)
