---
title: Seznam typů entit – LUIS
titleSuffix: Azure Cognitive Services
description: Seznam entit představuje pevně uzavřenou sadu příbuzných slov spolu s jejich synonymy. LUIS nezjistí další hodnoty pro entity seznamu. Pomocí funkce doporučit můžete zobrazit návrhy nových slov na základě aktuálního seznamu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1757faf8ab2be0b62956b6939ee068929f9275a4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695239"
---
# <a name="list-entity"></a>Seznam entit 

Seznam entit představuje pevně uzavřenou sadu příbuzných slov spolu s jejich synonymy. LUIS nezjistí další hodnoty pro entity seznamu. Pomocí funkce **doporučit** můžete zobrazit návrhy nových slov na základě aktuálního seznamu. Pokud existuje více než jedna entita seznamu se stejnou hodnotou, Každá entita se vrátí v dotazu koncového bodu. 

Entita seznamu není zjištěna počítačem. Je to přesně shoda textu. LUIS označí jakoukoli shodu s položkou v libovolném seznamu jako entitu v odpovědi. 

**Entita je vhodná, když jsou textová data:**

* Jsou známá sada.
* Nemění se často. Pokud potřebujete seznam často změnit, nebo chcete, aby se seznam automaticky rozšířil, je lepší volbou jednoduchá entita se seznamem frází. 
* Sada nepřekračuje maximální [hranice](luis-boundaries.md) Luis pro tento typ entity.
* Text v utterance je přesná shoda s synonymem nebo kanonickým názvem. LUIS nepoužívá seznam nad rámec přesně vyhovujících textů. Přibližná shoda, nerozlišování velkých a malých písmen, odvozování, plural a jiné varianty nejsou vyřešeny entitou seznamu. Chcete-li spravovat variace, zvažte použití [vzoru](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) s volitelnou syntaxí textu.

![Seznam entit](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Ukázkový kód JSON

Předpokládejme, že aplikace obsahuje seznam s názvem `Cities`, který umožňuje variace názvů měst včetně města letiště (mořských TAC), kódu letiště (moře), poštovního směrovacího čísla (98101) a kódu telefonní oblasti (206).

|Položka seznamu|Synonyma položky|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

V předchozím utterance je slovo `paris` namapováno na Paříž položku jako součást entity seznamu `Cities`. Entita seznamu odpovídá normalizovanému názvu položky i synonymům položky.

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


Toto je JSON, pokud je v řetězci dotazu nastavená hodnota `verbose=false`:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Toto je JSON, pokud je v řetězci dotazu nastavená hodnota `verbose=true`:

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

V tomto [kurzu](luis-quickstart-intent-and-list-entity.md)se naučíte používat **entitu seznam** k extrakci přesných shod textu ze seznamu známých položek. 
