---
title: Jednoduchý typ entity - LUIS
titleSuffix: Azure Cognitive Services
description: Jednoduchá entita popisuje jeden koncept z kontextu naučené počítače. Přidejte seznam frází při použití jednoduché entity ke zlepšení výsledků.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 8b92aa6057c81ec9442372c5b85918cb92196d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74894759"
---
# <a name="simple-entity"></a>Jednoduchá entita

Jednoduchá entita je obecná entita, která popisuje jeden koncept a je se naučil z kontextu naučil počítače. Vzhledem k tomu, že jednoduché entity jsou obecně názvy, jako jsou názvy společností, názvy produktů nebo jiné kategorie názvů, přidejte [seznam frází](luis-concept-feature.md) při použití jednoduché entity k posílení signálu použitých názvů.

**Entita se hodí, když:**

* Data nejsou konzistentně formátována, ale označují totéž.

![jednoduchá entita](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Příklad JSON

`Bob Jones wants 3 meatball pho`

V předchozí utterance `Bob Jones` je označen jako `Customer` jednoduchá entita.

Data vrácená z koncového bodu zahrnují název entity, zjištěný text z utterance, umístění zjištěného textu a skóre:

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

Toto je JSON, pokud `verbose=false` je nastavena v řetězci dotazu:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Datový objekt|Název entity|Hodnota|
|--|--|--|
|Jednoduchá entita|`Customer`|`bob jones`|

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučit se syntaxi vzoru](reference-pattern-syntax.md)