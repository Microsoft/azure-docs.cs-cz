---
title: Telefonní číslo Předem sestavené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o předem sestavené entitě telefonního čísla v jazyce Porozumění (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270473"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Předdefinovaná entita telefonního čísla pro aplikaci LUIS
Subjekt `phonenumber` extrahuje různé telefonní čísla včetně kódu země. Vzhledem k tomu, že tato entita je již trénovaný, není nutné přidat příklad projevy do aplikace. Entita `phonenumber` je `en-us` podporována pouze v jazykové verzi.

## <a name="types-of-a-phone-number"></a>Typy telefonního čísla
`Phonenumber`je spravován z úložiště GitHub [s textem pro rozpoznávání](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Řešení pro tuto předem připravenou entitu

Pro dotaz jsou vráceny následující objekty entity:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je `verbose` s parametrem nastaveným na `false`:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/V3-verbose)
Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

Následující příklad ukazuje rozlišení entity **builtin.phonenumber.**

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Informace o [procentech](luis-reference-prebuilt-percentage.md), [číslech](luis-reference-prebuilt-number.md)a [teplotách.](luis-reference-prebuilt-temperature.md)
