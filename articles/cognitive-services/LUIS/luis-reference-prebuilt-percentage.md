---
title: Procento předem sestavené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje procentuální údaje předem připravené entity v Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: bb64a32e2bdd3976fba3ce63433b13eb4891afc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541692"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Procentuální předem sestavená entita pro aplikaci pro LUIS
Procentuální čísla se můžou vyskytovat jako zlomky, `3 1/2` nebo jako procenta `2%` . Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující procento. Procentuální hodnota entity je podporována v [mnoha jazykových verzích](luis-reference-prebuilt-entities.md).

## <a name="types-of-percentage"></a>Typy procent
Procento je spravované z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) v GitHubu.

## <a name="resolution-for-prebuilt-percentage-entity"></a>Řešení pro předem sestavenou entitu procent

Pro dotaz se vrátí následující objekty entity:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující kód JSON je s `verbose` parametrem nastaveným na `false` :

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/V3-verbose)
Následující kód JSON je s `verbose` parametrem nastaveným na `true` :

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
#### <a name="v2-response"></a>[Odpověď v2](#tab/V2)

Následující příklad ukazuje řešení entity **Builtin. PERCENTAGE** .

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

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si o [pořadí](luis-reference-prebuilt-ordinal.md), [čísle](luis-reference-prebuilt-number.md)a [teplotních](luis-reference-prebuilt-temperature.md) entitách.
