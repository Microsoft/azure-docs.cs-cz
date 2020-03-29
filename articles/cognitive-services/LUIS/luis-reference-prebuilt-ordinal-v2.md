---
title: Ordinal V2 předbudová entita - LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje ordinal V2 předem informace o entitě v porozumění jazykům (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270482"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Ordinal V2 předem sestavená entita pro aplikaci LUIS
Číslo číslo Číslo 2 rozšiřuje [řadové](luis-reference-prebuilt-ordinal.md) číslo tak, aby `next`poskytovalo relativní odkazy, například , `last`a `previous`. Ty nejsou extrahovány pomocí ordinální předem sestavené entity.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Rozlišení pro předbudová ordinální entitu V2

Pro dotaz jsou vráceny následující objekty entity:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je `verbose` s parametrem nastaveným na `false`:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/V3-verbose)

Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
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

Následující příklad ukazuje rozlišení entity **builtin.ordinalV2.**

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Seznamte se s [procenty](luis-reference-prebuilt-percentage.md), [telefonním číslem](luis-reference-prebuilt-phonenumber.md)a [teplotními](luis-reference-prebuilt-temperature.md) entitami.
