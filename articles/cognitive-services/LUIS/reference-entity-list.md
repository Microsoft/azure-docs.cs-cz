---
title: Typ entity seznamu – LUIS
description: Seznam entit představují pevnou, uzavřenou sadu souvisejících slov spolu s jejich synonymy. Služba LUIS nezjišťuje další hodnoty pro entity seznamu. Pomocí funkce Doporučit můžete zobrazit návrhy nových slov na základě aktuálního seznamu.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 795d16bc2e0c4223ff3ac283a72493923d3ab355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297233"
---
# <a name="list-entity"></a>Entita seznamu

Seznam entit představují pevnou, uzavřenou sadu souvisejících slov spolu s jejich synonymy. Služba LUIS nezjišťuje další hodnoty pro entity seznamu. Pomocí funkce **Doporučit** můžete zobrazit návrhy nových slov na základě aktuálního seznamu. Pokud existuje více než jeden seznam entity se stejnou hodnotou, každá entita je vrácena v dotazu koncového bodu.

Entita seznamu se neučí strojově. Je to přesná shoda textu. Služba LUIS označí libovolnou shodu s položkou v libovolném seznamu jako entitu v odpovědi.

**Entita se hodí, když textová data:**

* Jsou známé sady.
* Moc se nemění. Pokud potřebujete seznam často měnit nebo chcete, aby se seznam rozbalil sám, je lepší volbou jednoduchá entita posílená seznamem frází.
* Tato sada nepřekračuje maximální [hranice](luis-boundaries.md) aplikace LUIS pro tento typ entity.
* Text v utterance je malá a velká písmena shoda se synonymem nebo kanonický název. Služba LUIS nepoužívá seznam mimo shodu. Přibližné párování, vyplývající, množné číslo a další varianty nejsou vyřešeny s entitou seznamu. Chcete-li spravovat varianty, zvažte použití [vzoru](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) s volitelnou syntaxí textu.

![entita seznamu](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Příklad .json pro import do entity seznamu

  Hodnoty můžete importovat do existující entity seznamu pomocí následujícího formátu json:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>Příklad odpovědi JSON

Předpokládejme, že aplikace `Cities`má seznam s názvem , umožňující varianty názvů měst včetně města letiště (Sea-tac), číslo letiště (SEA), PSČ (98101) a telefonní směrové číslo (206).

|Položka seznamu|Synonyma položky|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

V předchozí utterance slovo `paris` je mapována na položku `Cities` paříž jako součást entity seznamu. Entita seznamu odpovídá normalizovanému názvu položky i synonymům položky.

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)


Toto je JSON, pokud `verbose=false` je nastavena v řetězci dotazu:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Toto je JSON, pokud `verbose=true` je nastavena v řetězci dotazu:

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
|Entita seznamu|`Cities`|`paris`|


## <a name="next-steps"></a>Další kroky

V tomto [kurzu](tutorial-list-entity.md)se dozvíte, jak pomocí **entity seznamu** extrahovat přesné shody textu ze seznamu známých položek.
