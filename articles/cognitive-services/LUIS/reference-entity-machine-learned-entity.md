---
title: Typ entity navoděné počítačem – LUIS
titleSuffix: Azure Cognitive Services
description: Počítačově učená entita je upřednostňovanou entitou pro vytváření aplikací LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: aac4ba3ec63d425cac782f5db65bba923d24ed71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75551994"
---
# <a name="machine-learned-entity"></a>Strojově naučená entita

Počítačově učená entita je upřednostňovanou entitou pro vytváření aplikací LUIS.


## <a name="example-json"></a>Příklad JSON

Předpokládejme, že aplikace přijímá objednávky pizzy, jako je [například kurz rozložitelné entity](tutorial-machine-learned-entity.md). Každá objednávka může obsahovat několik různých pizz, včetně různých velikostí.

Mezi ukázkové promluvy patří:

|Příklad projevy pro aplikaci pizza|
|--|
|`Can I get a pepperoni pizza and a can of coke please`|
|`can I get a small pizza with onions peppers and olives`|
|`pickup an extra large meat lovers pizza`|



#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

Vzhledem k tomu, že entita nazdu se naučila stroj, může mít mnoho dílčích součástí s omezeními a popisovači, je to pouze příklad. To by mělo být považováno za vodítko pro to, co vaše entita vrátí.

Zvažte dotaz:

`deliver 1 large cheese pizza on thin crust and 2 medium pepperoni pizzas on deep dish crust`

Toto je JSON, pokud `verbose=false` je nastavena v řetězci dotazu:

```json
"entities": {
    "Order": [
        {
            "FullPizzaWithModifiers": [
                {
                    "PizzaType": [
                        "cheese pizza"
                    ],
                    "Size": [
                        [
                            "Large"
                        ]
                    ],
                    "Quantity": [
                        1
                    ]
                },
                {
                    "PizzaType": [
                        "pepperoni pizzas"
                    ],
                    "Size": [
                        [
                            "Medium"
                        ]
                    ],
                    "Quantity": [
                        2
                    ],
                    "Crust": [
                        [
                            "Deep Dish"
                        ]
                    ]
                }
            ]
        }
    ],
    "ToppingList": [
        [
            "Cheese"
        ],
        [
            "Pepperoni"
        ]
    ],
    "CrustList": [
        [
            "Thin"
        ]
    ]
}

```

Toto je JSON, pokud `verbose=true` je nastavena v řetězci dotazu:

```json
"entities": {
    "Order": [
        {
            "FullPizzaWithModifiers": [
                {
                    "PizzaType": [
                        "cheese pizza"
                    ],
                    "Size": [
                        [
                            "Large"
                        ]
                    ],
                    "Quantity": [
                        1
                    ],
                    "$instance": {
                        "PizzaType": [
                            {
                                "type": "PizzaType",
                                "text": "cheese pizza",
                                "startIndex": 16,
                                "length": 12,
                                "score": 0.999998868,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Size": [
                            {
                                "type": "SizeList",
                                "text": "large",
                                "startIndex": 10,
                                "length": 5,
                                "score": 0.998720646,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Quantity": [
                            {
                                "type": "builtin.number",
                                "text": "1",
                                "startIndex": 8,
                                "length": 1,
                                "score": 0.999878645,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ]
                    }
                },
                {
                    "PizzaType": [
                        "pepperoni pizzas"
                    ],
                    "Size": [
                        [
                            "Medium"
                        ]
                    ],
                    "Quantity": [
                        2
                    ],
                    "Crust": [
                        [
                            "Deep Dish"
                        ]
                    ],
                    "$instance": {
                        "PizzaType": [
                            {
                                "type": "PizzaType",
                                "text": "pepperoni pizzas",
                                "startIndex": 56,
                                "length": 16,
                                "score": 0.999987066,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Size": [
                            {
                                "type": "SizeList",
                                "text": "medium",
                                "startIndex": 49,
                                "length": 6,
                                "score": 0.999841452,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Quantity": [
                            {
                                "type": "builtin.number",
                                "text": "2",
                                "startIndex": 47,
                                "length": 1,
                                "score": 0.9996054,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Crust": [
                            {
                                "type": "CrustList",
                                "text": "deep dish crust",
                                "startIndex": 76,
                                "length": 15,
                                "score": 0.761551,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ]
                    }
                }
            ],
            "$instance": {
                "FullPizzaWithModifiers": [
                    {
                        "type": "FullPizzaWithModifiers",
                        "text": "1 large cheese pizza on thin crust",
                        "startIndex": 8,
                        "length": 34,
                        "score": 0.616001546,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    },
                    {
                        "type": "FullPizzaWithModifiers",
                        "text": "2 medium pepperoni pizzas on deep dish crust",
                        "startIndex": 47,
                        "length": 44,
                        "score": 0.7395033,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "ToppingList": [
        [
            "Cheese"
        ],
        [
            "Pepperoni"
        ]
    ],
    "CrustList": [
        [
            "Thin"
        ]
    ],
    "$instance": {
        "Order": [
            {
                "type": "Order",
                "text": "1 large cheese pizza on thin crust and 2 medium pepperoni pizzas on deep dish crust",
                "startIndex": 8,
                "length": 83,
                "score": 0.6881274,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ToppingList": [
            {
                "type": "ToppingList",
                "text": "cheese",
                "startIndex": 16,
                "length": 6,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 56,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "CrustList": [
            {
                "type": "CrustList",
                "text": "thin crust",
                "startIndex": 32,
                "length": 10,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

Tato entita není k dispozici v běhu predikce V2.
* * *

## <a name="next-steps"></a>Další kroky

Další informace o entitě naučené strojem, včetně [výukového programu](tutorial-machine-learned-entity.md), [konceptů](luis-concept-entity-types.md#design-entities-for-decomposition)a [návodu](luis-how-to-add-entities.md#create-a-machine-learned-entity).

Informace o entitě [seznamu](reference-entity-list.md) a entitě [regulárních výrazů.](reference-entity-regular-expression.md)