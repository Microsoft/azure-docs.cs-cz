---
title: Ordinální předem sestavená entita – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje pořadí předem sestavených informací o entitách v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b2a2d9e78a0b152da14bb737079cf0dfdef0dc05
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491235"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Pořadí předem sestavené entity pro aplikaci LUIS
Ordinální číslo je numerická reprezentace objektu uvnitř sady: `first`, `second``third`. Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující ordinální číslo. V [mnoha kulturách](luis-reference-prebuilt-entities.md)je podporována ordinální entita. 

## <a name="types-of-ordinal"></a>Typy pořadových čísel
Ordinální číslo je spravované z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) v GitHubu.

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Řešení pro předem sestavenou ordinální entitu

Pro dotaz se vrátí následující objekty entity:

`Order the second option`

#### <a name="v3-responsetabv3"></a>[Odpověď V3](#tab/V3)

Následující JSON je s parametrem `verbose` nastaveným na `false`:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Podrobná odpověď V3](#tab/V3-verbose)
Následující JSON je s parametrem `verbose` nastaveným na `true`:

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
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

Následující příklad ukazuje řešení entity **Builtin. Ordinal** .

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * * 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si informace o [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [telefonním číslu](luis-reference-prebuilt-phonenumber.md)a [teplotních](luis-reference-prebuilt-temperature.md) entitách. 
