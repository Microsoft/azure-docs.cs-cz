---
title: Metoda CalcHistogram v Academic Knowledge API | Microsoft Docs
description: Pomocí této metody CalcHistogram vypočítat distribuci hodnot atributů pro sadu entit dokumentu v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: e0b773fb9791ee638c8cfdbbc9dca40543e50ec0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342379"
---
# <a name="calchistogram-method"></a>CalcHistogram – metoda

**Calchistogram** REST API se používá k výpočtu distribuci hodnoty atributů pro sadu entit dokumentu.          


**Koncový bod REST:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>
  
## <a name="request-parameters"></a>Parametry žádosti

Název  |Hodnota | Povinné?  |Popis
-----------|----------|--------|----------
**Expr**    |Textový řetězec | Ano  |Výraz dotazu, který určuje entity, za které chcete vypočítat histogramy.
**model** |Textový řetězec | Ne |Vyberte název modelu, který chcete dotaz.  V současné době má výchozí hodnotu *nejnovější*.
**Atributy** | Textový řetězec | Ne<br>Výchozí hodnota: | Seznam s položkami oddělenými čárkou, který určuje hodnoty atributů, které jsou zahrnuty v odpovědi. Názvy atributů rozlišují velká a malá písmena.
**počet** |Číslo | Ne<br>Výchozí: 10 |Počet výsledků vrátit.
**Posun**  |Číslo | Ne<br>Výchozí: 0 |Index prvního výsledku vrátit.
<br>
## <a name="response-json"></a>Odpověď (JSON)
Název | Popis
--------|---------
**Expr**  |Parametr expr z požadavku.
**num_entities** | Celkový počet odpovídajících entit.
**histogramy** |  Pole histogramy, jednu pro každý atribut v požadavku.
**.attribute histogramy [x]** | Název atributu, za které se spočítala histogramu.
**.distinct_values histogramy [x]** | Počet jedinečných hodnot mezi odpovídající entity pro tento atribut.
**.total_count histogramy [x]** | Celkový počet instancí hodnotu mezi odpovídající entity pro tento atribut.
**.histogram histogramy [x]** | Histogram data pro tento atribut.
**histogramy [.value .histogram [y] x]** |  Hodnotu pro atribut.
**histogramy [.logprob .histogram [y] x]**  |Celkový počet přirozené protokolu pravděpodobnost odpovídající entity, hodnota tohoto atributu.
**histogramy [.count .histogram [y] x]**  |Počet odpovídajících entit s hodnota tohoto atributu.
**byl zrušen** | Hodnota TRUE, pokud vypršel časový limit požadavku.

 <br>
#### <a name="example"></a>Příklad:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>V tomto příkladu, aby bylo možné generovat histogram počtu publikace podle roku pro konkrétní Autor od 2010, můžete nejdřív se vygeneruje dotaz pomocí výrazu **interpretovat** rozhraní API pomocí řetězec dotazu: *papíry podle jaime teevan po 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Výraz v první výklad, který je vrácen z interpret rozhraní API *a (složené (AA. AuN == 'jaime teevan'), Y > 2012)*.
<br>Tato hodnota výrazu je pak předané do **calchistogram** rozhraní API. *Attributes=Y,F.FN* parametr označuje, že distribuce dokumentu počty by měla být například podle roku a pole studie:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Odpověď na tuto žádost nejprve znamená, že se 37 dokumenty Paper, které odpovídají výrazu dotazu.  Pro *roku* atributu, je 3 jedinečných hodnot, jednu pro každý rok po 2012 (tj. 2013, 2014 a 2015) zadaný v dotazu.  Celkový počet dokumentu počet přes 3 odlišné hodnoty je 37.  Pro každou *roku*, histogram zobrazuje hodnoty, celková fyzická protokolu pravděpodobnosti a počet odpovídajících entit.     

Histogram pro *pole prostudovali* uvádí, že jsou 34 odlišné pole studie. Dokument může být přidružen více polí studii, celkový počet (53) může mít větší než počet odpovídajících entit.  I když existují 34 jedinečných hodnot, odpověď obsahuje pouze horní 4 z důvodu *počet = 4* parametr.

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
