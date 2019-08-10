---
title: Předem připravené entity adresy URL – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje adresu url informace předem připravených entit v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b05b4f8638eedc0830c887da59c0c22706a1c4ce
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933391"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita URL pro aplikaci LUIS
Adresa URL entity extrahuje adresy URL s názvy domény nebo IP adresy. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující adresy URL do aplikace. Adresa URL entity se podporuje v `en-us` pouze jazykovou verzi. 

## <a name="types-of-urls"></a>Typy adres URL
Adresa URL se spravuje z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) v GitHubu.

## <a name="resolution-for-prebuilt-url-entity"></a>Řešení pro předem připravených entit adresy URL

### <a name="api-version-2x"></a>Rozhraní API verze 2. x

Následující příklad ukazuje rozlišení **builtin.url** entity.

```json
{
  "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "https://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Verze Preview rozhraní API verze 3. x

Následující kód JSON je s `verbose` parametrem nastaveným na: `false`

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
        "entities": {
            "url": [
                "https://www.luis.ai"
            ]
        }
    }
}
```

Následující kód JSON je s `verbose` parametrem nastaveným na: `true`

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
        "entities": {
            "url": [
                "https://www.luis.ai"
            ],
            "$instance": {
                "url": [
                    {
                        "type": "builtin.url",
                        "text": "https://www.luis.ai",
                        "startIndex": 0,
                        "length": 19,
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

Další informace o [ordinální](luis-reference-prebuilt-ordinal.md), [číslo](luis-reference-prebuilt-number.md), a [teploty](luis-reference-prebuilt-temperature.md) entity.
