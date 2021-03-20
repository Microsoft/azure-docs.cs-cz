---
title: Předem připravené entity telefonního čísla – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje telefonní číslo předem sestavených informací o entitách v Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 598ecaddbab3b70297a460521c7ec3386b390a8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535385"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita pro aplikaci pro LUIS s telefonním číslem
`phonenumber`Entita extrahuje celou řadu telefonních čísel včetně kódu země. Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do aplikace přidat příklad projevy. `phonenumber`Entita je podporována pouze v `en-us` jazykové verzi.

## <a name="types-of-a-phone-number"></a>Typy telefonního čísla
`Phonenumber` je spravováno z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) v GitHubu.

## <a name="resolution-for-this-prebuilt-entity"></a>Řešení pro tuto předem vytvořenou entitu

Pro dotaz se vrátí následující objekty entity:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující kód JSON je s `verbose` parametrem nastaveným na `false` :

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/V3-verbose)
Následující kód JSON je s `verbose` parametrem nastaveným na `true` :

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
#### <a name="v2-response"></a>[Odpověď v2](#tab/V2)

Následující příklad ukazuje řešení entity **Builtin. PhoneNumber** .

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

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si o [procentech](luis-reference-prebuilt-percentage.md), [číslech](luis-reference-prebuilt-number.md)a [teplotních](luis-reference-prebuilt-temperature.md) entitách.
