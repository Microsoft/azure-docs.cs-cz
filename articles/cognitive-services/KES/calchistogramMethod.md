---
title: Metoda CalcHistogram – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak použít metodu CalcHistogram v znalostní báze zkoumání služby (KES) rozhraní API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: f994a254e661cd245d2e953efd3bd595d50c6fc7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214309"
---
# <a name="calchistogram-method"></a>calchistogram – metoda
*Calchistogram* metoda vypočítá objektů, které odpovídají výrazu strukturovaných dotazů a vypočítá distribuci hodnot jejich atributů.

## <a name="request"></a>Žádost
`http://<host>/calchistogram?expr=<expr>[&options]` 

Name|Value|Popis
----|-----|-----------
expr | Textový řetězec | Výraz strukturovaných dotazů, který určuje index entity, přes která chcete vypočítat histogramy.
Atributy | Textový řetězec (výchozí = "") | Čárkami oddělený seznam atributu, který má zahrnout do odpovědi.
count   | Číslo (výchozí = 10) | Číslo s vrácenými výsledky.
Posun  | Číslo (výchozí = 0) | Index první výsledek vrátit.

## <a name="response-json"></a>Odpověď (JSON)
JSONPath | Popis
----|----
$.expr | *výraz* parametr z požadavku.
$.num_entities | Celkový počet odpovídajících entit.
$.histograms |  Pole histogramy, jeden pro každý požadovaný atribut.
$.histograms[\*].attribute | Název atributu, nad niž se spočítala histogram.
$.histograms[\*].distinct_values | Počet jedinečných hodnot mezi odpovídající entity pro tento atribut.
$.histograms[\*].total_count | Celkový počet instancí hodnotu mezi odpovídající entity pro tento atribut.
$.histograms[\*].histogram | Histogram data pro tento atribut.
$.histograms[\*].histogram[\*].value | Hodnota atributu.
$.histograms[\*].histogram[\*].logprob  | Celkový počet přirozený logaritmus pravděpodobnost odpovídající entity s touto hodnotou atributu.
$.histograms[\*].histogram[\*].count    | Počet odpovídajících entit s touto hodnotou atributu.
$.aborted | True, pokud vypršel časový limit žádosti.

### <a name="example"></a>Příklad:
V příkladu academic publikace následující vypočte histogram publikace počty podle year a klíčové slovo pro konkrétní Autor od 2013:

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

Z odpovědi vyplývá, že existuje 37 Paper odpovídající výraz dotazu.  Pro *rok* atribut, existují 3 různé hodnoty, jeden pro každý rok od 2013.  Celkový počet papíru počet za 3 různé hodnoty je 37.  Pro každou *rok*, histogram zobrazuje hodnotu, celkový počet přirozený logaritmus pravděpodobnosti a počtu odpovídajících entit.     

Histogram pro *– klíčové slovo* ukazuje, že jsou 34 jedinečných klíčových slov. Jak dokument může být spojen s více klíčových slov, celkový počet (53) může být větší než počet odpovídajících entit.  I když existují 34 jedinečných hodnot, odpověď obsahuje pouze horní 4 z důvodu "count = 4" parametr.

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
