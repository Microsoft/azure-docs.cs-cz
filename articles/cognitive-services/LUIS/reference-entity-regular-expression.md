---
title: Typ entity regulárního výrazu – LUIS
description: Regulární výraz je nejvhodnější pro nezpracovaný utterance text. Ignoruje velikost písmen a ignoruje kulturní variantu.  Pro porovnání regulárních výrazů se použije po změnách kontroly pravopisu na úrovni znaků, nikoli na úrovni tokenu.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 18e44ec43e1169aa054e6e5b4591ccd8611a7f4d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025221"
---
# <a name="regular-expression-entity"></a>Entiay regulárního výrazu

Entita regulárního výrazu extrahuje entitu na základě vzoru regulárního výrazu, který zadáte.

Regulární výraz je nejvhodnější pro nezpracovaný utterance text. Ignoruje velikost písmen a ignoruje kulturní variantu.  Pro porovnání regulárních výrazů se použije po změnách kontroly pravopisu na úrovni znaků, nikoli na úrovni tokenu. Pokud je regulární výraz příliš složitý, například při použití řady hranatých závorek, nemůžete do modelu přidat výraz. Používá součást, ale ne všechny knihovny [regulárních regulárních knihoven .NET](/dotnet/standard/base-types/regular-expressions) .

**Entita je vhodná v případě, že:**

* Data jsou konzistentně naformátovaná pomocí jakékoli variace, která je také konzistentní.
* Regulární výraz nepotřebuje více než 2 úrovně vnoření.

![Entiay regulárního výrazu](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Požadavky na využití

Regulární výrazy mohou odpovídat více, než očekáváte shodu. Příkladem je číselné porovnání slov, například `one` a `two` . Příkladem je následující regulární výraz, který odpovídá číslu `one` spolu s jinými čísly:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Tento výraz regulárního výrazu také odpovídá všem slovům, která končí těmito čísly, například `phone` . Aby bylo možné tyto problémy vyřešit, ujistěte se, že regulární výraz odpovídá hranicím slova v účtu. Regulární výraz pro použití hranice slov pro tento příklad se používá v následujícím regulárním výrazu:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Ukázkový kód JSON

Při použití `kb[0-9]{6}` jako definice entity regulárního výrazu je následující odpověď JSON příkladem utterance s vrácenými entitami regulárních výrazů pro dotaz:

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


Toto je kód JSON, pokud `verbose=false` je nastaven v řetězci dotazu:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Toto je kód JSON, pokud `verbose=true` je nastaven v řetězci dotazu:

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

Další informace o entitách:

* [Koncepty](luis-concept-entity-types.md)
* [Postup vytvoření](luis-how-to-add-entities.md)