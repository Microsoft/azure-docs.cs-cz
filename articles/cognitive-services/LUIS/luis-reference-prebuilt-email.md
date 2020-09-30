---
title: Referenční informace k e-mailu předdefinovaných entit LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje předem vytvořené informace o entitě e-mailu v Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 806357670bead54658f0b501ca20473293275d58
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533362"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita emailu pro aplikaci LUIS
Extrakce e-mailů zahrnuje celou e-mailovou adresu z utterance. Vzhledem k tomu, že je tato entita už vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující e-mail. E-mailová entita je podporována `en-us` pouze v jazykové verzi.

## <a name="resolution-for-prebuilt-email"></a>Řešení předem vytvořeného e-mailu

Pro dotaz se vrátí následující objekty entity:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující kód JSON je s `verbose` parametrem nastaveným na `false` :

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/V3-verbose)

Následující kód JSON je s `verbose` parametrem nastaveným na `true` :

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
#### <a name="v2-response"></a>[Odpověď v2](#tab/V2)

Následující příklad ukazuje řešení entity **Builtin. email** .

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

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si [číslo](luis-reference-prebuilt-number.md), [pořadové](luis-reference-prebuilt-ordinal.md)číslo a [procento](luis-reference-prebuilt-percentage.md).
