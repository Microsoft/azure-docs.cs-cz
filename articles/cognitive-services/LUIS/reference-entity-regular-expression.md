---
title: Typ entity regulárního výrazu – LUIS
titleSuffix: Azure Cognitive Services
description: Regulární výraz je nejvhodnější pro nezpracovaný utterance text. Ignoruje velikost písmen a ignoruje kulturní variant.  Porovnávání regulárních výrazů se použije po kontrolu pravopisu změny na úrovni znak není úroveň tokenu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841213"
---
# <a name="regular-expression-entity"></a>Entiay regulárního výrazu

Entita regulárního výrazu extrahuje entitu na základě vzoru regulárního výrazu, který zadáte.

Regulární výraz je nejvhodnější pro nezpracovaný utterance text. Ignoruje velikost písmen a ignoruje kulturní variant.  Porovnávání regulárních výrazů se použije po kontrolu pravopisu změny na úrovni znak není úroveň tokenu. Pokud je regulární výraz příliš složitý, například při použití řady hranatých závorek, nemůžete do modelu přidat výraz. Používá součást, ale ne všechny knihovny [regulárních regulárních knihoven .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) .

**Entita je vhodná v případě, že:**

* Data jsou konzistentně naformátovaná pomocí jakékoli variace, která je také konzistentní.
* Regulární výraz nepotřebuje více než 2 úrovně vnoření.

![Entiay regulárního výrazu](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Požadavky na využití

Regulární výrazy mohou odpovídat více, než očekáváte shodu. Příkladem je číselné porovnání slov, například `one` a `two`. Příkladem je následující regulární výraz, který odpovídá číslu `one` spolu s dalšími čísly:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Tento výraz regulárního výrazu také odpovídá všem slovům, která končí těmito čísly, například `phone`. Aby bylo možné tyto problémy vyřešit, ujistěte se, že regulární výraz odpovídá hranicím slova v účtu. Regulární výraz pro použití hranice slov pro tento příklad se používá v následujícím regulárním výrazu:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Ukázkový soubor JSON

Při použití `kb[0-9]{6}`jako definice entity regulárního výrazu je následující odpověď JSON příkladem utterance s vrácenými entitami regulárních výrazů pro dotaz:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

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


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)


Toto je JSON, pokud je v řetězci dotazu nastavená `verbose=false`:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Toto je JSON, pokud je v řetězci dotazu nastavená `verbose=true`:

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

V tomto [kurzu](tutorial-regex-entity.md)vytvoříte aplikaci, která extrahuje konzistentně formátovaná data z utterance pomocí entity **regulárního výrazu** .