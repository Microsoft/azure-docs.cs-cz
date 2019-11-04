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
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6f262752a50b58eae8ffbea81b8e7fc4d8c65b98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464978"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita emailu pro aplikaci LUIS
Extrakce e-mailů zahrnuje celou e-mailovou adresu z utterance. Vzhledem k tomu, že je tato entita už vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující e-mail. E-mailová entita je podporovaná jenom v `en-us` jazykové verzi. 

## <a name="resolution-for-prebuilt-email"></a>Řešení předem vytvořeného e-mailu

Pro dotaz se vrátí následující objekty entity:

`please send the information to patti@contoso.com`

#### <a name="v3-responsetabv3"></a>[Odpověď V3](#tab/V3)

Následující JSON je s parametrem `verbose` nastaveným na `false`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Podrobná odpověď V3](#tab/V3-verbose)

Následující JSON je s parametrem `verbose` nastaveným na `true`:

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
#### <a name="v2-responsetabv2"></a>[Odpověď v2](#tab/V2)

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
