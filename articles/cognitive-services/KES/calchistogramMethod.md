---
title: CalcHistogram metoda v rozhraní API služby zkoumání znalostní báze | Microsoft Docs
description: Zjistěte, jak lze pomocí této metody CalcHistogram v znalostní báze zkoumání služby (KES) rozhraní API v kognitivní služby.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 6ed694b0cc9cf41b815cc54b9f6d12adb2b7cd64
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342445"
---
# <a name="calchistogram-method"></a>calchistogram – metoda
*Calchistogram* metoda vypočítá objekty odpovídající výraz SQL a vypočítá distribuci jejich hodnot atributů.

## <a name="request"></a>Žádost
`http://<host>/calchistogram?expr=<expr>[&options]` 

Název|Hodnota|Popis
----|-----|-----------
Expr | Textový řetězec | Strukturované dotazu výraz, který určuje index entity, za které chcete vypočítat histogramy.
Atributy | Textový řetězec (výchozí = "") | Čárkami oddělený seznam atributu zahrnuty v odpovědi.
count   | Číslo (výchozí = 10) | Počet výsledků vrátit.
Posun  | Číslo (výchozí = 0) | Index prvního výsledku vrátit.

## <a name="response-json"></a>Odpověď (JSON)
JSONPath | Popis
----|----
$.expr | *Expr* parametr z požadavku.
$.num_entities | Celkový počet odpovídajících entit.
$.histograms |  Pole histogramy, jednu pro každý požadovaný atribut.
$.histograms [\*] .attribute | Název atributu, za které se spočítala histogramu.
$.histograms [\*] .distinct_values | Počet jedinečných hodnot mezi odpovídající entity pro tento atribut.
$.histograms [\*] .total_count | Celkový počet instancí hodnotu mezi odpovídající entity pro tento atribut.
$.histograms [\*] .histogram | Histogram data pro tento atribut.
$.histograms [\*] .histogram [\*] .value | Hodnota atributu.
$.histograms [\*] .histogram [\*] .logprob  | Celkový počet přirozené protokolu pravděpodobnost odpovídající entity, hodnota tohoto atributu.
$.histograms [\*] .histogram [\*] .count    | Počet odpovídajících entit s hodnota tohoto atributu.
$.aborted | Hodnota TRUE, pokud vypršel časový limit požadavku.

### <a name="example"></a>Příklad:
V příkladu academic publikace následující vypočítá histogram publikace počty podle roku a – klíčové slovo pro konkrétní Autor od 2013:

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

Z odpovědi vyplývá, že jsou dokumenty 37 Paper odpovídající výrazu dotazu.  Pro *roku* atributu, je 3 jedinečných hodnot, jednu pro každý rok od 2013.  Celkový počet dokumentu počet přes 3 odlišné hodnoty je 37.  Pro každou *roku*, histogram zobrazuje hodnoty, celková fyzická protokolu pravděpodobnosti a počet odpovídajících entit.     

Histogram pro *– klíčové slovo* uvádí, že jsou 34 klíčová slova distinct. Dokument může být přidružen více klíčová slova, celkový počet (53) může mít větší než počet odpovídajících entit.  I když existují 34 jedinečných hodnot, odpověď obsahuje pouze horní 4 z důvodu "count = 4" parametr.

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
