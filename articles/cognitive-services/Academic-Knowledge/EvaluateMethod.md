---
title: Vyhodnotit metodu – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Pomocí metody Evaluate můžete vrátit sadu akademických entit na základě výrazu dotazu.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 69e701d6727e5410b71e6cf8fbe20a1cd038ddb0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705014"
---
# <a name="evaluate-method"></a>Evaluate – metoda

**Vyhodnocení** REST API slouží k vrácení sady akademických entit na základě výrazu dotazu.
<br>

**Koncový bod REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>Parametry žádosti  

Name     | Value | Požadováno?  | Popis
-----------|-----------|---------|--------
**výrazu**       | Textový řetězec | Ano | Výraz dotazu určující entity, které mají být vráceny.
**model**      | Textový řetězec | Ne  | Název modelu, který chcete dotazovat.  V současné době je hodnota standardně *nejnovější*.        
**atribut** | Textový řetězec | Ne<br>výchozí Id | Seznam oddělený čárkami, který určuje hodnoty atributů, které jsou zahrnuty v odpovědi. V názvech atributů se rozlišují velká a malá písmena.
**count**        | Number | Ne<br>Výchozí: 10 | Počet výsledků, které se mají vrátit
**polohy**     | Number |   Ne<br>Výchozí: 0    | Index prvního výsledku, který se má vrátit
**OrderBy** |   Textový řetězec | Ne<br>Výchozí: snížením sondy | Název atributu, který se používá k řazení entit. Volitelně lze zadat vzestupné nebo sestupné řazení. Formát je: *Název: ASC* nebo *Název: DESC*.
  
 <br>

## <a name="response-json"></a>Odpověď (JSON)

Name | Popis
-------|-----   
**výrazu** |  Parametr *expr* z požadavku.
**podnikům** |  Pole o 0 nebo více entitách, které odpovídají výrazu dotazu. Každá entita obsahuje hodnotu přirozeného pravděpodobnosti protokolu a hodnoty jiných požadovaných atributů.
**aborted** | Hodnota true, pokud vypršel časový limit žádosti

<br>

#### <a name="example"></a>Příklad:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Výraz se obvykle získá z odpovědi na metodu **Interpretace** .  Můžete ale také vytvářet výrazy dotazu sami (viz [syntaxe výrazu dotazu](QueryExpressionSyntax.md)).  
  
Pomocí parametrů *Count* a *offset* může být získán velký počet výsledků bez odeslání jediné žádosti, která má za následek obrovský (a potenciálně pomalou) odpověď.  V tomto příkladu požadavek použil výraz pro první výklad z odpovědi rozhraní API interpreta jako hodnotu *expr* . Parametr *Count = 2* určuje, že jsou požadovány 2 výsledky entit. A *atributy = ČŘ, Y, CC, AA. AuN, AA. Parametr AuId* označuje, že pro každý výsledek se vyžadují název, rok, počet citací, jméno autora a ID autora.  Seznam atributů naleznete v tématu [atributy entity](EntityAttributes.md) .
  
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
 
