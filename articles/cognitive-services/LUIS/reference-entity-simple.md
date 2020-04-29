---
title: Jednoduchý typ entity – LUIS
titleSuffix: Azure Cognitive Services
description: Jednoduchá Entita popisuje jeden koncept z kontextu zjištěného počítačem. Přidejte seznam frází, když použijete jednoduchou entitu pro zlepšení výsledků.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 8b92aa6057c81ec9442372c5b85918cb92196d61
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74894759"
---
# <a name="simple-entity"></a>Jednoduchá entita

Jednoduchá entita je obecná entita, která popisuje jeden koncept a je získána z kontextu zjištěného počítačem. Vzhledem k tomu, že jednoduché entity jsou obecně názvy, například názvy společností, názvy produktů nebo jiné kategorie názvů, přidejte [seznam frází](luis-concept-feature.md) při použití jednoduché entity k posílení signálu používaných názvů.

**Entita je vhodná v případě, že:**

* Data nejsou konzistentně naformátovaná, ale označují stejnou věc.

![jednoduchá entita](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Ukázkový kód JSON

`Bob Jones wants 3 meatball pho`

V předchozím utterance `Bob Jones` je označen jako jednoduchá `Customer` entita.

Data vrácená z koncového bodu zahrnují název entity, zjištěný text z utterance, umístění zjištěného textu a skóre:

#### <a name="v2-prediction-endpoint-response"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Toto je kód JSON, `verbose=false` Pokud je nastaven v řetězci dotazu:

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
> [Informace o syntaxi vzorů](reference-pattern-syntax.md)