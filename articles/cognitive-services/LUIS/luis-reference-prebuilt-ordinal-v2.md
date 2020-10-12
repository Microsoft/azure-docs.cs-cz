---
title: Ordinální v2 předem sestavená entita – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje pořadí předem sestavených informací o entitách v Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 3fd80389dfa54cb8386c13e0e05a1e71ac4bd956
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541930"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Předdefinovaná entita pořadí v2 pro aplikaci pro LUIS
Pořadové číslo v2 rozbalí [pořadí](luis-reference-prebuilt-ordinal.md) , aby poskytoval relativní odkazy `next` , například, `last` a `previous` . Tyto nejsou extrahovány pomocí pořadí předem sestavené entity.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Řešení pro předem sestavenou ordinální entitu v2

Pro dotaz se vrátí následující objekty entity:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující kód JSON je s `verbose` parametrem nastaveným na `false` :

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

#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/V3-verbose)

Následující kód JSON je s `verbose` parametrem nastaveným na `true` :

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
#### <a name="v2-response"></a>[Odpověď v2](#tab/V2)

Následující příklad ukazuje řešení entity **Builtin. ordinalV2** .

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

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si o [procentech](luis-reference-prebuilt-percentage.md), [telefonním číslu](luis-reference-prebuilt-phonenumber.md)a o [teplotních](luis-reference-prebuilt-temperature.md) entitách.
