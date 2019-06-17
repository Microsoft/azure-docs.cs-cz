---
title: Teplota předem připravených entit
titleSuffix: Azure
description: Tento článek obsahuje teploty informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 8d5e10a7a4fe7fb12bc4140690fcabffa16b32be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072100"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Teplota předem připravených entit pro aplikace LUIS
Teplota extrahuje celou řadu typů teploty. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující teploty do aplikace. Teplota entity se podporuje v [mnoha jazykových verzí](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Typy teploty
Spravuje se z teplota [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) úložiště GitHub

## <a name="resolution-for-prebuilt-temperature-entity"></a>Řešení pro teploty předem připravených entit

### <a name="api-version-2x"></a>Verze rozhraní API 2.x

Následující příklad ukazuje rozlišení **builtin.temperature** entity.

```json
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
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
}
```

### <a name="preview-api-version-3x"></a>Verze Preview rozhraní API 3.x

Následující kód JSON je `verbose` parametr nastaven na `false`:

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
                }
            ]
        }
    }
}
```

Následující kód JSON je `verbose` parametr nastaven na `true`:

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
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
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Další postup

Další informace o [procento](luis-reference-prebuilt-percentage.md), [číslo](luis-reference-prebuilt-number.md), a [stáří](luis-reference-prebuilt-age.md) entity. 
