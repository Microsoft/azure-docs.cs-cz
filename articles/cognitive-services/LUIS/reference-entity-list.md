---
title: Seznam typů entit – LUIS
description: Seznam entit představuje pevně uzavřenou sadu příbuzných slov spolu s jejich synonymy. LUIS nezjistí další hodnoty pro entity seznamu. Pomocí funkce doporučit můžete zobrazit návrhy nových slov na základě aktuálního seznamu.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 410b33b5c6078d096fa4b2acaa7b49bc14c95e31
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608268"
---
# <a name="list-entity"></a>Entita seznamu

Seznam entit představuje pevně uzavřenou sadu příbuzných slov spolu s jejich synonymy. LUIS nezjistí další hodnoty pro entity seznamu. Pomocí funkce **doporučit** můžete zobrazit návrhy nových slov na základě aktuálního seznamu. Pokud existuje více než jedna entita seznamu se stejnou hodnotou, Každá entita se vrátí v dotazu koncového bodu.

Entita seznamu není zjištěna počítačem. Je to přesně shoda textu. LUIS označí jakoukoli shodu s položkou v libovolném seznamu jako entitu v odpovědi.

**Entita je vhodná, když jsou textová data:**

* Jsou známá sada.
* Nemění se často. Pokud potřebujete seznam často změnit, nebo chcete, aby se seznam automaticky rozšířil, je lepší volbou jednoduchá entita se seznamem frází.
* Tato sada nepřekračuje maximální [hranice](luis-limits.md) aplikace LUIS pro tento typ entity.
* Text v utterance se nerozlišuje bez rozlišení velkých a malých písmen s synonymem nebo kanonickým názvem. LUIS nepoužívá seznam za shodou. Nepřibližná shoda, odvozování, plural a jiné varianty nejsou vyřešeny entitou seznamu. Chcete-li spravovat variace, zvažte použití [vzoru](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) s volitelnou syntaxí textu.

![Seznam entit](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Příklad: JSON pro import do seznamu entit

  Hodnoty můžete importovat do existující entity seznamu pomocí následujícího formátu. JSON:

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

Předpokládejme, že aplikace obsahuje seznam s názvem `Cities` , který umožňuje variace názvů měst, včetně města letiště (mořských TAC), kódu letiště (moře), poštovního směrovacího čísla (98101) a kódu telefonní oblasti (206).

|Položka seznamu|Synonyma položky|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

V předchozím utterance je slovo v `paris` rámci entity seznamu namapováno na Paříž Item `Cities` . Entita seznamu odpovídá normalizovanému názvu položky i synonymům položky.

#### <a name="v2-prediction-endpoint-response"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Toto je kód JSON, pokud `verbose=false` je nastaven v řetězci dotazu:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Toto je kód JSON, pokud `verbose=true` je nastaven v řetězci dotazu:

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

Další informace o entitách:

* [Koncepty](luis-concept-entity-types.md)
* [Postup vytvoření](luis-how-to-add-entities.md)
