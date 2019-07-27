---
title: Referenční informace k e-mailu předdefinovaných entit LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje e-mailu předem připravených entit informace v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9c9c7b373f820dd23c70a67a1de8545935a1d93c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560247"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita emailu pro aplikaci LUIS
Extrakce e-mailu obsahuje celé e-mailovou adresu z utterance. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující e-mailu záměry aplikace. E-mailu entity se podporuje v `en-us` pouze jazykovou verzi. 

## <a name="resolution-for-prebuilt-email"></a>Řešení pro předem připravených e-mailu

### <a name="api-version-2x"></a>Rozhraní API verze 2. x

Následující příklad ukazuje rozlišení **builtin.email** entity.

```json
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55,
      "resolution": {
        "value": "patti.owens@microsoft.com"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Verze Preview rozhraní API verze 3. x

Následující kód JSON je s `verbose` parametrem nastaveným na: `false`

```json
{
    "query": "please send the information to patti.owens@microsoft.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti.owens@microsoft.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti.owens@microsoft.com"
            ]
        }
    }
}
```


Následující kód JSON je s `verbose` parametrem nastaveným na: `true`

```json
{
    "query": "please send the information to patti.owens@microsoft.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti.owens@microsoft.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti.owens@microsoft.com"
            ],
            "$instance": {
                "email": [
                    {
                        "type": "builtin.email",
                        "text": "patti.owens@microsoft.com",
                        "startIndex": 31,
                        "length": 25,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Další postup

Další informace o [číslo](luis-reference-prebuilt-number.md), [ordinální](luis-reference-prebuilt-ordinal.md), a [procento](luis-reference-prebuilt-percentage.md). 
