---
title: Typ entity regulárního výrazu – LUIS
titleSuffix: Azure Cognitive Services
description: Regulární výraz je nejvhodnější pro nezpracovaný utterance text. Ignoruje velikost písmen a ignoruje kulturní variantu.  Pro porovnání regulárních výrazů se použije po změnách kontroly pravopisu na úrovni znaků, nikoli na úrovni tokenu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74841213"
---
# <a name="regular-expression-entity"></a>Entiay regulárního výrazu

Entita regulárního výrazu extrahuje entitu na základě vzoru regulárního výrazu, který zadáte.

Regulární výraz je nejvhodnější pro nezpracovaný utterance text. Ignoruje velikost písmen a ignoruje kulturní variantu.  Pro porovnání regulárních výrazů se použije po změnách kontroly pravopisu na úrovni znaků, nikoli na úrovni tokenu. Pokud je regulární výraz příliš složitý, například při použití řady hranatých závorek, nemůžete do modelu přidat výraz. Používá součást, ale ne všechny knihovny [regulárních regulárních knihoven .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) .

**Entita je vhodná v případě, že:**

* Data jsou konzistentně naformátovaná pomocí jakékoli variace, která je také konzistentní.
* Regulární výraz nepotřebuje více než 2 úrovně vnoření.

![Entiay regulárního výrazu](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Požadavky na využití

Regulární výrazy mohou odpovídat více, než očekáváte shodu. Příkladem je číselné porovnání slov, například `one` a. `two` Příkladem je následující regulární výraz, který odpovídá číslu `one` spolu s jinými čísly:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Tento výraz regulárního výrazu také odpovídá všem slovům, která končí těmito čísly, například `phone`. Aby bylo možné tyto problémy vyřešit, ujistěte se, že regulární výraz odpovídá hranicím slova v účtu. Regulární výraz pro použití hranice slov pro tento příklad se používá v následujícím regulárním výrazu:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Ukázkový kód JSON

Při použití `kb[0-9]{6}`jako definice entity regulárního výrazu je následující odpověď JSON příkladem utterance s vrácenými entitami regulárních výrazů pro dotaz:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

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


#### <a name="v3-prediction-endpoint-response"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)


Toto je kód JSON, `verbose=false` Pokud je nastaven v řetězci dotazu:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Toto je kód JSON, `verbose=true` Pokud je nastaven v řetězci dotazu:

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