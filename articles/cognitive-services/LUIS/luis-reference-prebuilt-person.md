---
title: Předdefinovaná entita PersonName – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o předem sestavené entitě personName v jazyce Porozumění (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 768c719211e8a8f2133d3798343d076e795a3da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273421"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Předdefinovaná entita PersonName pro aplikaci LUIS
Předvytvořená entita personName detekuje jména osob. Vzhledem k tomu, že tato entita je již trénovaný, není nutné přidávat ukázkové projevy obsahující personName záměry aplikace. personName entita je podporována v anglické a čínské [jazykové verzi](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Řešení pro entitu personName

Pro dotaz jsou vráceny následující objekty entity:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)


Následující JSON je `verbose` s parametrem nastaveným na `false`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/V3-verbose)
Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[Odpověď V2](#tab/V2)

Následující příklad ukazuje rozlišení entity **builtin.personName.**

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Přečtěte si o [e-mailových](luis-reference-prebuilt-email.md), [číselných](luis-reference-prebuilt-number.md)a [řadových](luis-reference-prebuilt-ordinal.md) entitách.
