---
title: Referenční informace k e-mailu předdefinovaných entit LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje předem vytvořené informace o entitě e-mailu v Language Understanding (LUIS).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273487"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita emailu pro aplikaci LUIS
Extrakce e-mailů zahrnuje celou e-mailovou adresu z utterance. Vzhledem k tomu, že je tato entita už vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující e-mail. E-mailová entita `en-us` je podporována pouze v jazykové verzi.

## <a name="resolution-for-prebuilt-email"></a>Řešení předem vytvořeného e-mailu

Pro dotaz se vrátí následující objekty entity:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující kód JSON je s `verbose` parametrem nastaveným `false`na:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/V3-verbose)

Následující kód JSON je s `verbose` parametrem nastaveným `true`na:

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
