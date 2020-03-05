---
title: Předem vytvořená entita stáří – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje stáří informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 1607b80276a9c35bf0ac2f0a00bd2226e2c07f97
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270799"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Předem připravená entita pro aplikaci pro LUIS
Stáří předem připravených entit zachycuje hodnotu stáří jak číselně a co se týče dnů, týdnů, měsíců a roků. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující stáří na záměry aplikace. Věková entita je podporována v [mnoha jazykových verzích](luis-reference-prebuilt-entities.md).

## <a name="types-of-age"></a>Typy věku
Stáří je spravované z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) v GitHubu.

## <a name="resolution-for-prebuilt-age-entity"></a>Řešení pro stáří předem připravených entit



#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je s parametrem `verbose` nastaveným na `false`:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/V3-verbose)
Následující JSON je s parametrem `verbose` nastaveným na `true`:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ],
    "$instance": {
        "age": [
            {
                "type": "builtin.age",
                "text": "90 day old",
                "startIndex": 2,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Odpověď v2](#tab/V2)

Následující příklad ukazuje řešení entity **Builtin. Age** .

```json
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
```
* * *

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Seznamte se s entitami [Currency](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)a [Dimension](luis-reference-prebuilt-dimension.md) .
