---
title: Procento předem sestavené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje procentuální údaje předem připravené entity v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 9e9aa3fc7c0ff76f6b93a8acc67681d1ed7cf4ad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491233"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Procentuální předem sestavená entita pro aplikaci pro LUIS
Procentuální čísla se můžou vyskytovat jako zlomky, `3 1/2`nebo jako procento `2%`. Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující procento. Procentuální hodnota entity je podporována v [mnoha jazykových verzích](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Typy procent
Procento je spravované z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) v GitHubu.

## <a name="resolution-for-prebuilt-percentage-entity"></a>Řešení pro předem sestavenou entitu procent

Pro dotaz se vrátí následující objekty entity:

`set a trigger when my stock goes up 2%`

#### <a name="v3-responsetabv3"></a>[Odpověď V3](#tab/V3)

Následující JSON je s parametrem `verbose` nastaveným na `false`:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Podrobná odpověď V3](#tab/V3-verbose)
Následující JSON je s parametrem `verbose` nastaveným na `true`:

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
#### <a name="v2-responsetabv2"></a>[Odpověď v2](#tab/V2)

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
