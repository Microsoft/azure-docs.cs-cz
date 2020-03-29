---
title: Typ entity regulárního výrazu – LUIS
titleSuffix: Azure Cognitive Services
description: Regulární výraz je nejvhodnější pro nezpracovaný text utterance. Ignoruje případ a ignoruje kulturní variantu.  Porovnávání regulárních výrazů se použije po změnách kontroly pravopisu na úrovni znaků, nikoli na úrovni tokenu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74841213"
---
# <a name="regular-expression-entity"></a>Entiay regulárního výrazu

Entita regulárního výrazu extrahuje entitu na základě zadaný vzor regulárního výrazu.

Regulární výraz je nejvhodnější pro nezpracovaný text utterance. Ignoruje případ a ignoruje kulturní variantu.  Porovnávání regulárních výrazů se použije po změnách kontroly pravopisu na úrovni znaků, nikoli na úrovni tokenu. Pokud je regulární výraz příliš složitý, například pomocí mnoha závorek, nemůžete výraz přidat do modelu. Používá část, ale ne všechny [knihovny .NET Regex.](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)

**Entita se hodí, když:**

* Data jsou konzistentně formátována s libovolnou variantou, která je také konzistentní.
* Regulární výraz nepotřebuje více než 2 úrovně vnoření.

![Entiay regulárního výrazu](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Aspekty použití

Regulární výrazy se mohou shodovat s více, než očekáváte. Příkladem je číselné slovo odpovídající `one` `two`jako a . Příkladem je následující regulární výraz, `one` který odpovídá číslu spolu s dalšími čísly:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Tento výraz regulárního výrazu také odpovídá `phone`všem slovům, která končí těmito čísly, například . Chcete-li opravit problémy, jako je tento, ujistěte se, že regulární výraz zápasy bere v úvahu hranice slova. Regulární výraz pro použití word hranice pro tento příklad se používá v následujícím regulárním výrazu:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Příklad JSON

Při `kb[0-9]{6}`použití aplikace jako definice entity regulárního výrazu je následující odpověď JSON příkladem utterance s vrácenými entitami regulárních výrazů pro dotaz:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)


Toto je JSON, pokud `verbose=false` je nastavena v řetězci dotazu:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Toto je JSON, pokud `verbose=true` je nastavena v řetězci dotazu:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Další kroky

V tomto [kurzu](tutorial-regex-entity.md)vytvořte aplikaci pro extrahování konzistentně formátovaných dat z utterance pomocí entity **regulární výraz.**