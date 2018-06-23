---
title: Vyhodnocení metody v Academic Knowledge API | Microsoft Docs
description: Pomocí této metody Evaluate vrátit sadu academic entit na základě výrazu dotazu v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 3005ae1f6df042a49db086de4982d8206f6938a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342365"
---
# <a name="evaluate-method"></a>Vyhodnocení – metoda

**Vyhodnotit** REST API se používá k vrácení sady academic entit na základě výrazu dotazu.
<br>

**Koncový bod REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>
## <a name="request-parameters"></a>Parametry žádosti  
Název     | Hodnota | Povinné?  | Popis
-----------|-----------|---------|--------
**Expr**       | Textový řetězec | Ano | Výraz dotazu, který určuje entit, které má být vrácen.
**model**      | Textový řetězec | Ne  | Název modelu, který chcete dotaz.  V současné době má výchozí hodnotu *nejnovější*.        
**Atributy** | Textový řetězec | Ne<br>Výchozí: Id | Seznam s položkami oddělenými čárkou, který určuje hodnoty atributů, které jsou zahrnuty v odpovědi. Názvy atributů rozlišují velká a malá písmena.
**počet**        | Číslo | Ne<br>Výchozí: 10 | Počet výsledků vrátit.
**Posun**     | Číslo |   Ne<br>Výchozí: 0    | Index prvního výsledku vrátit.
**Řadit podle** |   Textový řetězec | Ne<br>Výchozí hodnota: snížením prob | Název atributu, který se používá pro řazení entity. Volitelně můžete pořadí lze zadat. Formát je: *název: asc* nebo *název: desc*.
  
 <br>
## <a name="response-json"></a>Odpověď (JSON)
Název | Popis
-------|-----   
**Expr** |  *Expr* parametr z požadavku.
**entity** |  Pole 0 nebo více entit, které odpovídá výrazu dotazu. Každá entita obsahuje hodnotu pravděpodobnosti přirozené protokolu a hodnoty další požadované atributy.
**byl zrušen** | Hodnota TRUE, pokud vypršel časový limit požadavku.

<br>
#### <a name="example"></a>Příklad:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Obvykle bude výraz získán z odpověď **interpretovat** metoda.  Ale můžete také vytvořit výrazy dotazů sami (viz [syntaxe výrazu dotazu](QueryExpressionSyntax.md)).  
  
Pomocí *počet* a *posun* parametry, velkého počtu výsledků lze získat bez odeslání této odpovědi výsledky v velký (a potenciálně pomalu) jeden požadavek.  V tomto příkladu používá požadavek výraz pro první interpretace z **interpretovat** jako odpověď rozhraní API *expr* hodnotu. *Počet = 2* parametr určuje, že se vyžadují 2 výsledky entity. A *atributy = Ti, Y, kopie, AA. AuN, AA. AuId* parametr označuje, že název, rok, počet citací, jméno autora a Autor ID se pro každý výsledek.  V tématu [atributům Entity](EntityAttributes.md) pro seznam atributů.
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
