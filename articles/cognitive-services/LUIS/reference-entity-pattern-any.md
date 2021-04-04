---
title: Vzor. libovolný typ entity – LUIS
titleSuffix: Azure Cognitive Services
description: Pattern. any je zástupný symbol s proměnlivou délkou, který se používá jenom v šabloně vzoru utterance k označení, kde začíná a končí entita.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: ec23be3709cebc534c059a21c52452abff683b18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "91542202"
---
# <a name="patternany-entity"></a>Entita Pattern.any

Pattern. any je zástupný symbol s proměnlivou délkou, který se používá jenom v šabloně vzoru utterance k označení, kde začíná a končí entita.  

Vzor. všechny entity musí být označeny v příkladech šablony [vzoru](luis-how-to-model-intent-pattern.md) , nikoli v příkladech uživatele záměr.

**Entita je vhodná v případě, že:**

* Koncová entita může být zaměněna se zbývajícím textem utterance.

## <a name="usage"></a>Využití

Pro klientskou aplikaci, která vyhledává knihy na základě názvu, vzoru. vše extrahuje úplný název. Šablona utterance pomocí vzoru. Any pro toto hledání v knihách je `Was {BookTitle} written by an American this year[?]` .

V následující tabulce má každý řádek dvě verze utterance. Horní utterance je to, jak LUIS zpočátku vidí utterance. Není jasné, kde začíná a končí nadpis knihy. Dolní utterance používá vzor. kterákoli entita označuje začátek a konec entity.

|Utterance s entitou tučně|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Jednalo **se o člověka, který v tomto roce nedokázal své manželky na Hat a další klinické úkony** zapsané americkým?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Byl tento rok v roce Pajamas zapsaný na **polovinu režimu spánku** ?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Jednalo **se o konkrétní smuteký dort: nové** zapsané do USA tento rok?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**Ve svém kapesním počítači je Wocket!** Napsali jste tento rok American?|
||



## <a name="example-json"></a>Ukázkový kód JSON

Zamyslete se nad následujícím dotazem:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

S názvem vloženého formuláře, který se má extrahovat jako vzor. any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

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


#### <a name="v3-prediction-endpoint-response"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Toto je kód JSON, pokud `verbose=false` je nastaven v řetězci dotazu:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Toto je kód JSON, pokud `verbose=true` je nastaven v řetězci dotazu:

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

V tomto [kurzu](luis-tutorial-pattern.md)použijte **vzor. libovolnou** entitu pro extrahování dat z projevy, kde jsou projevy ve správném formátu a kde se konce dat můžou snadno zaměňovat se zbývajícími slovy utterance.
