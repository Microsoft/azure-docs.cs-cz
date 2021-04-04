---
title: Ordinální předem sestavená entita – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje pořadí předem sestavených informací o entitách v Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 1fa86c8960ea9f32163ebd7991260a19ef7a5d79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535453"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Pořadí předem sestavené entity pro aplikaci LUIS
Ordinální číslo je numerická reprezentace objektu v rámci sady: `first` , `second` , `third` . Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující ordinální číslo. V [mnoha kulturách](luis-reference-prebuilt-entities.md)je podporována ordinální entita.

## <a name="types-of-ordinal"></a>Typy pořadových čísel
Ordinální číslo je spravované z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) v GitHubu.

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Řešení pro předem sestavenou ordinální entitu

Pro dotaz se vrátí následující objekty entity:

`Order the second option`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující kód JSON je s `verbose` parametrem nastaveným na `false` :

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/V3-verbose)
Následující kód JSON je s `verbose` parametrem nastaveným na `true` :

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

#### <a name="v2-response"></a>[Odpověď v2](#tab/V2)

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
