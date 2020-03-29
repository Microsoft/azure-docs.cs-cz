---
title: Type entity Pattern.any – LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any je zástupný symbol proměnné délky používaný pouze v utterance šablony vzoru k označení místa, kde entita začíná a končí.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75979165"
---
# <a name="patternany-entity"></a>Entita Pattern.any

Pattern.any je zástupný symbol proměnné délky používaný pouze v utterance šablony vzoru k označení místa, kde entita začíná a končí.  

Pattern.any entity musí být označeny v příkladech šablony [Pattern,](luis-how-to-model-intent-pattern.md) nikoli v příkladech záměru uživatele.

**Entita se hodí, když:**

* Koncovka entity může být zaměňována se zbývajícím textem utterance.

## <a name="usage"></a>Využití

Vzhledem k tomu, klientská aplikace, která hledá knihy na základě názvu, pattern.any extrahuje celý název. Promluva šablony pomocí pattern.any pro `Was {BookTitle} written by an American this year[?]`toto hledání knihy je .

V následující tabulce má každý řádek dvě verze utterance. Horní utterance je, jak LUIS zpočátku vidí utterance. Není jasné, kde název knihy začíná a končí. Dolní utterance používá Pattern.any entity označit začátek a konec entity.

|Utterance s entitou tučně|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Byl **muž, který si spletl svou ženu za klobouk a další klinické příběhy** napsal Američan v tomto roce?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Byl **Half Asleep v žabím pyžamu** napsaný američanem letos?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Byl **zvláštní smutek Lemon Cake: Román** napsal Američan v tomto roce?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**Byl tam wocket v mé kapse!** napsal američan v tomto roce?|
||



## <a name="example-json"></a>Příklad JSON

Zamyslete se nad následujícím dotazem:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

S vloženým názvem formuláře, který chcete extrahovat jako Pattern.any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

Toto je JSON, pokud `verbose=false` je nastavena v řetězci dotazu:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Toto je JSON, pokud `verbose=true` je nastavena v řetězci dotazu:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
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

V tomto [kurzu](luis-tutorial-pattern.md)použijte **Pattern.any** entity extrahovat data z projevy, kde projevy jsou dobře formátovány a kde konec dat lze snadno zaměnit se zbývající slova utterance.
