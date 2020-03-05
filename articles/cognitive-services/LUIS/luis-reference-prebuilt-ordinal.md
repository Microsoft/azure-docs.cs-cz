---
title: Ordinální předem sestavená entita – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o pořadovém místě předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bb3bb27db48255f534e873ed4e93ac62f07016af
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273446"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Pořadí předem sestavené entity pro aplikaci LUIS
Ordinální číslo je numerická reprezentace objektu uvnitř sady: `first`, `second``third`. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující ordinální záměry aplikace. V [mnoha kulturách](luis-reference-prebuilt-entities.md)je podporována ordinální entita.

## <a name="types-of-ordinal"></a>Typy pořadí
Ordinální číslo je spravované z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) v GitHubu.

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Řešení pro předem připravených entit podle pořadového čísla

Pro dotaz se vrátí následující objekty entity:

`Order the second option`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je s parametrem `verbose` nastaveným na `false`:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/V3-verbose)
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
