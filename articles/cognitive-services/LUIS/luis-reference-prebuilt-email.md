---
title: Odkaz na e-maily předdefinovaných entit luis
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o e-mailových předem vytvořených entitách v jazyce Porozumění (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f7aa2a341a82d30f7022af49e626a3cd358854b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273487"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Elektronická entita s předdefinovanými e-maily pro aplikaci LUIS
Extrakce e-mailu zahrnuje celou e-mailovou adresu z utterance. Vzhledem k tomu, že tato entita je již trénovaný, není nutné přidávat příklad projevy obsahující e-mail y záměry aplikace. E-mailová `en-us` entita je podporována pouze v jazykové verzi.

## <a name="resolution-for-prebuilt-email"></a>Rozlišení pro předem sestavený e-mail

Pro dotaz jsou vráceny následující objekty entity:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je `verbose` s parametrem nastaveným na `false`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/V3-verbose)

Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
                "length": 17,
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

Následující příklad ukazuje rozlišení entity **builtin.email.**

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Informace o [počtu](luis-reference-prebuilt-number.md), [řadové](luis-reference-prebuilt-ordinal.md)maješky a [procentuální](luis-reference-prebuilt-percentage.md).
