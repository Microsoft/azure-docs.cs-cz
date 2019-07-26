---
title: Seznam typů entit
titleSuffix: Language Understanding - Azure Cognitive Services
description: Seznam entit představuje pevně uzavřenou sadu příbuzných slov spolu s jejich synonymy. Služba LUIS nevyhledává další hodnoty pro seznam entit. Pomocí funkce doporučit můžete zobrazit návrhy nových slov na základě aktuálního seznamu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: c278a72327d476be8963b10db5e8231b6d859a4a
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480184"
---
# <a name="list-entity"></a>Entita seznamu 

Seznam entit představuje pevně uzavřenou sadu příbuzných slov spolu s jejich synonymy. Služba LUIS nevyhledává další hodnoty pro seznam entit. Použití **doporučujeme** funkce návrhy pro nové slova na základě aktuálního seznamu. Pokud existuje více než jednu entitu seznamu se stejnou hodnotou, je každá entita vrácené dotazem koncový bod. 

Entita seznamu není zjištěna počítačem. Se neshoduje přesný text. Služba LUIS označí všechny shody jako položka v seznamu jako entity v odpovědi. 

**Entita je vhodná, když jsou textová data:**

* Jsou známá sada.
* Nemění se často. Pokud potřebujete seznam často změnit, nebo chcete, aby se seznam automaticky rozšířil, je lepší volbou jednoduchá entita se seznamem frází. 
* Tato sada nepřekračuje maximální [hranice](luis-boundaries.md) aplikace LUIS pro tento typ entity.
* Text promluvy se přesně shoduje se synonymem nebo názvem v kanonickém tvaru. LUIS nepoužívá seznam nad rámec přesně vyhovujících textů. Přibližná shoda, nerozlišování velkých a malých písmen, odvozování, plural a jiné varianty nejsou vyřešeny entitou seznamu. Chcete-li spravovat variace, zvažte použití [vzoru](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) s volitelnou syntaxí textu.

![seznam entit](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Ukázkový soubor JSON

Předpokládejme, že aplikace má seznam s názvem `Cities`, což každodenně názvy měst včetně město letiště (Sea hrají), kód letiště (SEA), poštovní směrovací číslo poštovní směrovací (98101) a phone oblasti kódu (206).

|Položka seznamu|Synonyma položky|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

V předchozím utterance slovo `paris` je namapována na Paříž položky jako součást `Cities` seznam entit. Seznam entit odpovídá normalizovaný název položky i synonyma položky.

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

Jiný příklad utterance pomocí synonymum pro Paříž:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

|Datový objekt|Název entity|Hodnota|
|--|--|--|
|Jednoduché Entity|`Customer`|`bob jones`|

## <a name="next-steps"></a>Další kroky

V tomto [kurzu](luis-quickstart-intent-and-list-entity.md)se naučíte používat entitu **seznam** k extrakci přesných shod textu ze seznamu známých položek. 
