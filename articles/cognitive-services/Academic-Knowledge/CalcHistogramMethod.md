---
title: Metoda CalcHistogram-Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Použijte metodu CalcHistogram pro výpočet distribuce hodnot atributů pro sadu papírových entit.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 9e84b1ad37b3224ec5553d0a66ba0fc84bc88f55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705069"
---
# <a name="calchistogram-method"></a>Metoda CalcHistogram

REST API **calchistogram** se používá k výpočtu distribuce hodnot atributů pro sadu papírových entit.          


**Koncový bod REST:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>Parametry žádosti

Name  |Value | Požadováno?  |Popis
-----------|----------|--------|----------
**výrazu**    |Textový řetězec | Ano  |Výraz dotazu, který určuje entity, pro které se mají vypočítat histogramy.
**model** |Textový řetězec | Ne |Vyberte název modelu, který se má dotazovat.  V současné době je hodnota standardně *nejnovější*.
**atribut** | Textový řetězec | Ne<br>výchozí | Seznam oddělený čárkami, který určuje hodnoty atributů, které jsou zahrnuty v odpovědi. V názvech atributů se rozlišují velká a malá písmena.
**count** |Number | Ne<br>Výchozí: 10 |Počet výsledků, které se mají vrátit
**polohy**  |Number | Ne<br>Výchozí: 0 |Index prvního výsledku, který se má vrátit
**prodlev**  |Number | Ne<br>Výchozí: 1000 |Časový limit v milisekundách Budou vráceny pouze interprety, které byly nalezeny před uplynutím časového limitu.

## <a name="response-json"></a>Odpověď (JSON)

Name | Popis
--------|---------
**výrazu**  |Parametr expr z požadavku.
**num_entities** | Celkový počet vyhovujících entit
**histograms** |  Pole histogramů, jeden pro každý atribut zadaný v požadavku.
**histograms[x].attribute** | Název atributu, přes který byl histogram vypočítán.
**histograms[x].distinct_values** | Počet jedinečných hodnot mezi vyhovujícími entitami pro tento atribut.
**histograms[x].total_count** | Celkový počet instancí hodnot mezi vyhovujícími entitami pro tento atribut.
**histograms[x].histogram** | Data histogramu pro tento atribut.
**histograms[x].histogram[y].value** |  Hodnota atributu.
**histograms[x].histogram[y].logprob**  |Celková pravděpodobnost, jakou je přirozený protokol u vyhovujících entit s touto hodnotou atributu
**histograms[x].histogram[y].count**  |Počet vyhovujících entit s touto hodnotou atributu
**aborted** | Hodnota true, pokud vypršel časový limit žádosti


#### <a name="example"></a>Příklad:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>V tomto příkladu je možné vygenerovat histogram počtu publikací po rocích od určitého autora od 2010. nejdřív můžete vygenerovat výraz dotazu pomocí rozhraní API pro interpretaci s řetězcem dotazu: *Paper by espinosy teevan po 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Výraz v první interpretaci, který je vrácen z rozhraní Interpret API, je *a (složený (AA). AuN = = ' Espinosy teevan '), Y > 2012)* .
<br>Tato hodnota výrazu je pak předána do rozhraní **calchistogram** API. Parametr *Attributes = Y, F. FN* znamená, že distribuce počtů papíru by měla být podle roku a pole studie, např.:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Odpověď na tento požadavek nejprve označuje, že jsou 37 papíry, které odpovídají výrazu dotazu.  Pro atribut *year* je k dispozici 3 jedinečné hodnoty, jedna pro každý rok po 2012 (tj. 2013, 2014 a 2015), jak je uvedeno v dotazu.  Celkový počet papírů v rámci 3 jedinečných hodnot je 37.  V každém *roce*Histogram Zobrazuje hodnotu, celkovou pravděpodobnost přirozeného protokolu a počet vyhovujících entit.     

Histogram pro *pole studie* ukazuje, že existuje 34 různých polí studia. V případě, že je možné přidružit k více polím studie, celkový počet (53) může být větší než počet vyhovujících entit.  I když jsou 34 odlišné hodnoty, odpověď obsahuje jenom prvních 4, protože parametr *Count = 4* .

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
