---
title: Předem vytvořená entita person – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje předem připravené informace o entitách v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9777c62d97c70d4f6a0d0a4d912dea3fa8decd23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499540"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Předem sestavená entita person pro aplikaci LUIS
Entita s předdefinovaným jménem detekuje osoby. Vzhledem k tomu, že je tato entita již vyškolená, nemusíte do záměrů aplikace přidat příklad projevy obsahující jméno osoby. entita person je podporovaná v anglické a čínské [jazykové verzi](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Překlad pro entitu Person

Pro dotaz se vrátí následující objekty entity:

`Is Jill Jones in Cairo?`


#### <a name="v3-responsetabv3"></a>[Odpověď V3](#tab/V3)


Následující JSON je s parametrem `verbose` nastaveným na `false`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Podrobná odpověď V3](#tab/V3-verbose)
Následující JSON je s parametrem `verbose` nastaveným na `true`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-responsetabv2"></a>[Odpověď v2](#tab/V2)

Následující příklad ukazuje řešení entity **Builtin. person** .

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * * 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si informace o [e-mailu](luis-reference-prebuilt-email.md), [číslu](luis-reference-prebuilt-number.md)a [řadových](luis-reference-prebuilt-ordinal.md) entitách. 
