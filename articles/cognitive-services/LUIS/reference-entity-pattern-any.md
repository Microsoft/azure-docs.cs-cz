---
title: Vzor. libovolný typ entity – LUIS
titleSuffix: Azure Cognitive Services
description: Pattern. any je zástupný symbol s proměnlivou délkou, který se používá jenom v šabloně vzoru utterance k označení, kde začíná a končí entita.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: cda6c724a36a73dc34c2bf8e7158e3e3ec92d46b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563222"
---
# <a name="patternany-entity"></a>Pattern.Any entity 

Pattern. any je zástupný symbol s proměnlivou délkou, který se používá jenom v šabloně vzoru utterance k označení, kde začíná a končí entita.  

Vzor. všechny entity musí být označeny v příkladech šablony [vzoru](luis-how-to-model-intent-pattern.md) , nikoli v příkladech uživatele záměr.

**Entita je vhodná v případě, že:**

* Koncová entita může být zaměněna se zbývajícím textem utterance. 

## <a name="usage"></a>Použití

Pro klientskou aplikaci, která vyhledává knihy na základě názvu, vzoru. vše extrahuje úplný název. Šablona utterance pomocí vzoru. Any pro toto hledání v knihách `Was {BookTitle} written by an American this year[?]`je. 

V následující tabulce má každý řádek dvě verze utterance. Horní utterance je to, jak LUIS zpočátku vidí utterance. Není jasné, kde začíná a končí nadpis knihy. Dolní utterance používá vzor. kterákoli entita označuje začátek a konec entity. 

|Utterance s entitou tučně|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Jednalo **se o člověka, který v tomto roce nedokázal své manželky na Hat a další klinické úkony** zapsané americkým?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Byl tento rok v roce Pajamas zapsaný na **polovinu režimu spánku** ?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Byl **konkrétní smutek dortu: Jako novou** zapsanou v tomto roce American?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**Ve svém kapesním počítači je Wocket!** Napsali jste tento rok American?|
||

## <a name="example-json"></a>Ukázkový soubor JSON

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

V tomto [kurzu](luis-tutorial-pattern-any.md)použijte **vzor. libovolnou** entitu pro extrahování dat z projevy, kde jsou projevy ve správném formátu a kde se konce dat můžou snadno zaměňovat se zbývajícími slovy utterance.