---
title: Předem připravené entity telefonního čísla – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace telefonním čísle předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: bf2fd053cabeaf85b177e284f86ba378e0e4389e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933423"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita pro aplikaci pro LUIS s telefonním číslem
`phonenumber` Entity extrahuje širokou škálu telefonní čísla, včetně směrové číslo země. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy do aplikace. `phonenumber` Entity se podporuje v `en-us` pouze jazykovou verzi. 

## <a name="types-of-a-phone-number"></a>Typy telefonního čísla
`Phonenumber`je spravováno z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) v GitHubu.

## <a name="resolution-for-this-prebuilt-entity"></a>Řešení pro tuto předem vytvořenou entitu

### <a name="api-version-2x"></a>Rozhraní API verze 2. x

Následující příklad ukazuje rozlišení **builtin.phonenumber** entity.

```json
{
  "query": "my mobile is 1 (800) 642-7676",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Verze Preview rozhraní API verze 3. x

Následující kód JSON je s `verbose` parametrem nastaveným na: `false`

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

Následující kód JSON je s `verbose` parametrem nastaveným na: `true`

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ],
            "$instance": {
                "phonenumber": [
                    {
                        "type": "builtin.phonenumber",
                        "text": "1 (800) 642-7676",
                        "startIndex": 13,
                        "length": 16,
                        "score": 1,
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

Další informace o [procento](luis-reference-prebuilt-percentage.md), [číslo](luis-reference-prebuilt-number.md), a [teploty](luis-reference-prebuilt-temperature.md) entity. 
