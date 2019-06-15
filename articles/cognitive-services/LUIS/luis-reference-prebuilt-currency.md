---
title: Předem připravených entit měny
titleSuffix: Azure
description: Tento článek obsahuje měny informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 7e882a66ae5a090e1fd3a0850ff35281dc4e692d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072010"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Měna předem připravených entit pro aplikace LUIS
Měny předem připravených entit rozpozná měny v mnoha označení a země nebo oblasti, bez ohledu na jazykovou verzi aplikace LUIS. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující Měna, která se záměry aplikace. Entita měny se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Typy měny
Spravuje se z měny [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) úložiště GitHub

## <a name="resolution-for-currency-entity"></a>Řešení pro entitu měny

### <a name="api-version-2x"></a>Verze rozhraní API 2.x

Následující příklad ukazuje rozlišení **builtin.currency** entity.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```



### <a name="preview-api-version-3x"></a>Verze Preview rozhraní API 3.x

Následující kód JSON je `verbose` parametr nastaven na `false`:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ]
        }
    }
}
```

Následující kód JSON je `verbose` parametr nastaven na `true`:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ],
            "$instance": {
                "money": [
                    {
                        "type": "builtin.currency",
                        "text": "$10.99",
                        "startIndex": 23,
                        "length": 6,
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

Další informace o [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimenze](luis-reference-prebuilt-dimension.md), a [e-mailu](luis-reference-prebuilt-email.md) entity. 
