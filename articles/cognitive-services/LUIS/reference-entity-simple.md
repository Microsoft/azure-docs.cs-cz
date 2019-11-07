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
ms.openlocfilehash: 27eb4d827c1488b0d051505e62286fd66d6a286b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73671502"
---
# <a name="simple-entity"></a>Jednoduchá entita 

Jednoduchá entita je obecná entita, která popisuje jeden koncept a je získána z kontextu zjištěného počítačem. Vzhledem k tomu, že jednoduché entity jsou obecně názvy, například názvy společností, názvy produktů nebo jiné kategorie názvů, přidejte [seznam frází](luis-concept-feature.md) při použití jednoduché entity k posílení signálu používaných názvů. 

**Entita je vhodná v případě, že:**

* Data nejsou konzistentně naformátovaná, ale označují stejnou věc. 

![jednoduchá entita](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Ukázkový kód JSON

`Bob Jones wants 3 meatball pho`

V předchozím utterance je `Bob Jones` označena jako jednoduchá `Customer` entita.

Data vrácená z koncového bodu zahrnují název entity, zjištěný text z utterance, umístění zjištěného textu a skóre:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Toto je JSON, pokud je v řetězci dotazu nastavená `verbose=false`:

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

V tomto [kurzu](luis-quickstart-primary-and-secondary-data.md)extrahujete pomocí **jednoduché entity**data z pracovní úlohy, která se naučila z utterance. Chcete-li zvýšit přesnost extrakce, přidejte [seznam frází](luis-concept-feature.md) , které jsou specifické pro jednoduchou entitu.