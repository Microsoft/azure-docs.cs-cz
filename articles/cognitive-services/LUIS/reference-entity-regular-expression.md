---
title: Typ entity regulárního výrazu
titleSuffix: Language Understanding - Azure Cognitive Services
description: Regulární výraz je nejvhodnější pro nezpracovaný utterance text. Ignoruje velikost písmen a ignoruje kulturní variant.  Porovnávání regulárních výrazů se použije po kontrolu pravopisu změny na úrovni znak není úroveň tokenu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: dfffea26ccd8dfcecea2c6a2e07234f3ab27f72b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480228"
---
# <a name="regular-expression-entity"></a>Entiay regulárního výrazu 

Entita regulárního výrazu extrahuje entitu na základě vzoru regulárního výrazu, který zadáte.

Regulární výraz je nejvhodnější pro nezpracovaný utterance text. Ignoruje velikost písmen a ignoruje kulturní variant.  Porovnávání regulárních výrazů se použije po kontrolu pravopisu změny na úrovni znak není úroveň tokenu. Pokud je regulární výraz příliš složitý, například při použití řady hranatých závorek, nemůžete do modelu přidat výraz. Používá součást, ale ne všechny knihovny [regulárních regulárních knihoven .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) . 

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

### <a name="example-json"></a>Ukázkový soubor JSON

Při použití `kb[0-9]{6}`jako definice entity regulárního výrazu je následující odpověď JSON příkladem utterance s vrácenými entitami regulárních výrazů pro dotaz `When was kb123456 published?`:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

V tomto [kurzu](luis-quickstart-intents-regex-entity.md)vytvoříte aplikaci, která extrahuje konzistentně formátovaná data z utterance pomocí entity regulárního **výrazu** .