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
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 49f145ce3e9022826abad5d274dd611bb2cc6530
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270354"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita URL pro aplikaci LUIS
Adresa URL entity extrahuje adresy URL s názvy domény nebo IP adresy. Protože tato entita je už vytrénovaný, není potřeba přidat příklad projevy obsahující adresy URL do aplikace. Entita adresy URL je podporována pouze v `en-us` jazykové verzi.

## <a name="types-of-urls"></a>Typy adres URL
Adresa URL se spravuje z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) v GitHubu.

## <a name="resolution-for-prebuilt-url-entity"></a>Řešení pro předem připravených entit adresy URL

Pro dotaz se vrátí následující objekty entity:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[Odpověď V3](#tab/V3)

Následující JSON je s parametrem `verbose` nastaveným na `false`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Podrobná odpověď V3](#tab/V3-verbose)

Následující JSON je s parametrem `verbose` nastaveným na `true`:

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
#### <a name="v2-response"></a>[Odpověď v2](#tab/V2)

Následující příklad ukazuje řešení https://www.luis.ai je skvělým příkladem uměle se službou pro rozpoznávání umělých poznatků.

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

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si o [pořadí](luis-reference-prebuilt-ordinal.md), [čísle](luis-reference-prebuilt-number.md)a [teplotních](luis-reference-prebuilt-temperature.md) entitách.
