---
title: Vyhodnocení metody – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Pomocí metody vyhodnotit vrátit sadu akademické entity, které jsou založené na výrazu dotazu.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: c3449d3b61c9b8950c0530590e7f1950c06afbc3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187288"
---
# <a name="evaluate-method"></a>vyhodnocení metody

**Vyhodnotit** rozhraní REST API se používá k vrácení sady akademické entity, které jsou založené na výrazu dotazu.
<br>

**Koncový bod REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>
## <a name="request-parameters"></a>Parametry žádosti  
Název     | Hodnota | Povinné?  | Popis
-----------|-----------|---------|--------
**výraz**       | Textový řetězec | Ano | Výraz dotazu, který určuje entit, které má být vrácen.
**model**      | Textový řetězec | Ne  | Název modelu, který chcete dotazovat.  V současné době má výchozí hodnotu *nejnovější*.        
**Atributy** | Textový řetězec | Ne<br>Výchozí hodnota: ID | Čárkami oddělený seznam, který určuje hodnoty atributů, které jsou zahrnuty v odpovědi. Názvy atributů rozlišují malá a velká písmena.
**count**        | Číslo | Ne<br>Výchozí: 10 | Číslo s vrácenými výsledky.
**Posun**     | Číslo |   Ne<br>Výchozí: 0    | Index první výsledek vrátit.
**Řadit podle** |   Textový řetězec | Ne<br>Výchozí hodnota: snížením prob | Název atributu, který se používá pro řazení entity. Volitelně můžete Vzestupně/sestupně lze zadat. Formát je: *název: asc* nebo *název: desc*.
  
 <br>
## <a name="response-json"></a>Odpověď (JSON)
Název | Popis
-------|-----   
**výraz** |  *Expr* parametr z požadavku.
**Entity** |  Pole 0 nebo více entit, které odpovídají výrazu dotazu. Každá entita obsahuje přirozený logaritmus hodnota pravděpodobnosti a hodnot jiných požadovaných atributů.
**aborted** | True, pokud vypršel časový limit žádosti.

<br>
#### <a name="example"></a>Příklad:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Obvykle bude výraz získán z odpovědí **interpretovat** metody.  Můžete také vytvářet – výrazy dotazů sami, ale (viz [syntaxe výrazu dotazu](QueryExpressionSyntax.md)).  
  
Použití *počet* a *posun* parametry, velký počet výsledky mohou být získány bez odeslání jeden požadavek tuto odpověď výsledky v velkým (a potenciálně pomalé).  V tomto příkladu požadavek používá výraz pro první výklad z **interpretovat** odpověď rozhraní API jako *expr* hodnotu. *Počet = 2* parametr určuje, že jsou požadovány 2 výsledky entit. A *atributy = Ti, Y, kopie, AA. AuN, AA. AuId* parametr označuje, že název, rok, počet citace, jméno autora a Autor ID jsou požadovány pro každého výsledku.  Zobrazit [atributů Entity](EntityAttributes.md) seznam atributů.
  
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
 
