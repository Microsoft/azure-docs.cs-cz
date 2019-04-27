---
title: Zeměpisné oblasti V2 předem připravených entit
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje geographyV2 informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 17f612f2ee6c7d27dcec9f72ed3df1ed418eb3d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712596"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>GeographyV2 předem připravených entit pro aplikace LUIS
GeographyV2 předem připravených entit rozpozná místech. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující GeographyV2 záměry aplikace. GeographyV2 entity se podporuje v angličtině [jazykovou verzi](luis-reference-prebuilt-entities.md).

## <a name="subtypes"></a>Podtypy
Zeměpisné umístění mít podtypy:

|Podtyp|Účel|
|--|--|
|`poi`|bodu zájmu|
|`city`|název města|
|`countryRegion`|Název země nebo oblasti|
|`continent`|Název kontinent|
|`state`|Název státu nebo provincie|


## <a name="resolution-for-geographyv2-entity"></a>Řešení pro entitu GeographyV2
Následující příklad ukazuje rozlišení **builtin.geographyV2** entity.

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>Další postup

Další informace o [e-mailu](luis-reference-prebuilt-email.md), [číslo](luis-reference-prebuilt-number.md), a [ordinální](luis-reference-prebuilt-ordinal.md) entity. 
