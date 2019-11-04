---
title: Předem vytvořená jednotka – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o předem připravené entitě v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: f2ea08694419caaaf54e4fed45c7c1589be2473d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499506"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita pro aplikaci pro LUIS
Teplota extrahuje nejrůznější typy teplot. Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do aplikace přidat příklad projevy obsahující teplotu. V [mnoha jazykových verzích](luis-reference-prebuilt-entities.md)je podporována jednotka teploty. 

## <a name="types-of-temperature"></a>Typy teploty
Teplota se spravuje z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) v GitHubu.

## <a name="resolution-for-prebuilt-temperature-entity"></a>Řešení pro předem vytvořenou jednotku teploty

Pro dotaz se vrátí následující objekty entity:

`set the temperature to 30 degrees`


#### <a name="v3-responsetabv3"></a>[Odpověď V3](#tab/V3)

Následující JSON je s parametrem `verbose` nastaveným na `false`:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Podrobná odpověď V3](#tab/V3-verbose)
Následující JSON je s parametrem `verbose` nastaveným na `true`:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
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

Následující příklad ukazuje řešení entity **Builtin. teplota** .

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si o [procentech](luis-reference-prebuilt-percentage.md), [číslech](luis-reference-prebuilt-number.md)a [stáří](luis-reference-prebuilt-age.md) entit. 
