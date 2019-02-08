---
title: Metoda CalcHistogram – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Pomocí této metody CalcHistogram vypočítá distribuci hodnot atributů pro sadu entit papíru.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 1e922f1c02876f09dcf51b192bc08c4f4fccac9e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879016"
---
# <a name="calchistogram-method"></a>CalcHistogram – metoda

**Calchistogram** rozhraní REST API se používá k výpočtu distribuce hodnot atributů pro sadu entit papíru.          


**Koncový bod REST:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>
  
## <a name="request-parameters"></a>Parametry žádosti

Název  |Hodnota | Povinné?  |Popis
-----------|----------|--------|----------
**výraz**    |Textový řetězec | Ano  |Výraz dotazu, který určuje entity, přes která chcete vypočítat histogramy.
**model** |Textový řetězec | Ne |Vyberte název modelu, který chcete dotazovat.  V současné době má výchozí hodnotu *nejnovější*.
**Atributy** | Textový řetězec | Ne<br>Výchozí hodnota: | Čárkami oddělený seznam, který určuje hodnoty atributů, které jsou zahrnuty v odpovědi. Názvy atributů rozlišují malá a velká písmena.
**count** |Číslo | Ne<br>Výchozí: 10 |Číslo s vrácenými výsledky.
**Posun**  |Číslo | Ne<br>Výchozí: 0 |Index první výsledek vrátit.
**timeout**  |Číslo | Ne<br>Výchozí: 1000 |Časový limit v milisekundách. Jsou vráceny pouze interpretace nalezen předtím, než vypršel časový limit.

## <a name="response-json"></a>Odpověď (JSON)

Název | Popis
--------|---------
**výraz**  |Výraz parametru z požadavku.
**num_entities** | Celkový počet odpovídajících entit.
**histograms** |  Pole histogramy, jeden pro každý atribut v požadavku.
**histograms[x].attribute** | Název atributu, nad niž se spočítala histogram.
**histograms[x].distinct_values** | Počet jedinečných hodnot mezi odpovídající entity pro tento atribut.
**histograms[x].total_count** | Celkový počet instancí hodnotu mezi odpovídající entity pro tento atribut.
**histograms[x].histogram** | Histogram data pro tento atribut.
**histograms[x].histogram[y].value** |  Hodnota pro atribut.
**histograms[x].histogram[y].logprob**  |Celkový počet přirozený logaritmus pravděpodobnost odpovídající entity s touto hodnotou atributu.
**histograms[x].histogram[y].count**  |Počet odpovídajících entit s touto hodnotou atributu.
**aborted** | True, pokud vypršel časový limit žádosti.


#### <a name="example"></a>Příklad:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>V tomto příkladu, aby se vygenerovala histogram počtu publikace podle roku pro konkrétní Autor od 2010, můžete nejprve vygenerujeme pomocí výrazu dotazu **interpretovat** rozhraní API s řetězcem dotazu: *papíry podle jaime teevan po 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Výraz v první výklad, který je vrácen z interpretaci rozhraní API je *a (kompozitních (AA. AuN == "jaime teevan"), Y > 2012)*.
<br>Hodnota tohoto výrazu je pak předán **calchistogram** rozhraní API. *Attributes=Y,F.FN* parametr označuje, distribuce papíru počty by měl být například podle roku a pole studie:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Odpověď na tuto žádost nejdřív označuje, že jsou 37 papíry, které odpovídají výrazu dotazu.  Pro *rok* atribut, existují 3 různé hodnoty, jeden pro každý rok po 2012 (tj. 2013, 2014 a 2015), jak je uvedeno v dotazu.  Celkový počet papíru počet za 3 různé hodnoty je 37.  Pro každou *rok*, histogram zobrazuje hodnotu, celkový počet přirozený logaritmus pravděpodobnosti a počtu odpovídajících entit.     

Histogram pro *pole studovat* ukazuje, že jsou 34 různých polí studia. Jak dokument může být spojen s více polí studii, celkový počet (53) může být větší než počet odpovídajících entit.  I když existují 34 jedinečných hodnot, odpověď obsahuje pouze horní 4 z důvodu *počet = 4* parametru.

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
