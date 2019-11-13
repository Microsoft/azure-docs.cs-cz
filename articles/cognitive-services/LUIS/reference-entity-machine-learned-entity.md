---
title: Typ entity, která se naučila počítačem – LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017216"
---
# <a name="machine-learned-entity"></a>Entita získaná počítačem 



**Entita je vhodná, když jsou textová data:**


![seznam entit](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Ukázkový soubor JSON

Předpokládejme, že aplikace má seznam s názvem `Cities`, což každodenně názvy měst včetně město letiště (Sea hrají), kód letiště (SEA), poštovní směrovací číslo poštovní směrovací (98101) a phone oblasti kódu (206).

|Položka seznamu|Synonyma položky|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

V předchozím utterance slovo `paris` je namapována na Paříž položky jako součást `Cities` seznam entit. Seznam entit odpovídá normalizovaný název položky i synonyma položky.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)


Toto je JSON, pokud je v řetězci dotazu nastavená `verbose=false`:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Toto je JSON, pokud je v řetězci dotazu nastavená `verbose=true`:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
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

* * * 

|Datový objekt|Název entity|Hodnota|
|--|--|--|
|Seznam entit|`Cities`|`paris`|


## <a name="next-steps"></a>Další kroky

Přečtěte si informace o entitě [seznamu](reference-entity-list.md) a entitě [regulárního výrazu](reference-entity-regular-expression.md) .