---
title: Předem připravené entity adresy URL – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje předem připravené informace o entitách v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 12831ede2b9d9251f2e02fa396ee7d2fb2d61240
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499498"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Předem vytvořená entita URL pro aplikaci LUIS
Entita adresy URL extrahuje adresy URL s názvy domén nebo IP adresami. Vzhledem k tomu, že je tato entita již vyškolená, není nutné do aplikace přidat příklad projevy obsahující adresy URL. Entita adresy URL je podporována pouze v `en-us` jazykové verzi. 

## <a name="types-of-urls"></a>Typy adres URL
Adresa URL se spravuje z úložiště pro [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) v GitHubu.

## <a name="resolution-for-prebuilt-url-entity"></a>Řešení pro předem vytvořenou entitu URL

Pro dotaz se vrátí následující objekty entity:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-responsetabv3"></a>[Odpověď V3](#tab/V3)

Následující JSON je s parametrem `verbose` nastaveným na `false`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Podrobná odpověď V3](#tab/V3-verbose)

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
#### <a name="v2-responsetabv2"></a>[Odpověď v2](#tab/V2)

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
