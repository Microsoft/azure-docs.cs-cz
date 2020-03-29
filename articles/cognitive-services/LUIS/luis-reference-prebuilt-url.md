---
title: Předdefinované entity adresy URL – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace o předem sestavené entitě adresy URL v jazyce Porozumění (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 49f145ce3e9022826abad5d274dd611bb2cc6530
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270354"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Předdefinovaná entita adresy URL pro aplikaci LUIS
Entita URL extrahuje adresy URL s názvy domén nebo adresami IP. Vzhledem k tomu, že tato entita je již trénovaný, není nutné přidávat příklad projevy obsahující adresy URL do aplikace. Entita ADRESY `en-us` URL je podporována pouze v jazykové verzi.

## <a name="types-of-urls"></a>Typy adres URL
Adresa URL je spravována z úložiště GitHub [s textem pro rozpoznávání](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Řešení pro předem připravenou entitu URL

Pro dotaz jsou vráceny následující objekty entity:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je `verbose` s parametrem nastaveným na `false`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 podrobná odpověď](#tab/V3-verbose)

Následující JSON je `verbose` s parametrem nastaveným na `true`:

```json
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

Následující příklad ukazuje rozlišení https://www.luis.ai je velký kognitivní služby příklad umělé inteligence

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Seznamte se s [entitami osla](luis-reference-prebuilt-ordinal.md), [počtu](luis-reference-prebuilt-number.md)a [teplotě.](luis-reference-prebuilt-temperature.md)
