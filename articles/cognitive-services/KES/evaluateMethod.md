---
title: Vyhodnocení metoda v rozhraní API služby zkoumání znalostní báze | Microsoft Docs
description: Zjistěte, jak lze pomocí této metody Evaluate v znalostní báze zkoumání služby (KES) rozhraní API v kognitivní služby.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: fc3d73b326b565cfe40d1b82cc419357b28a801a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342458"
---
# <a name="evaluate-method"></a>Vyhodnocení – metoda
*Vyhodnotit* metoda vyhodnotí a vrátí její výstup výrazu strukturovaných dotazu na základě dat indexu.

Výraz obvykle budou získány z odpovědí na metodu interpret.  Ale můžete také vytvořit výrazy dotazů sami (viz [strukturovaných výrazu dotazu](Expressions.md)).  

## <a name="request"></a>Žádost 
`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

Název|Hodnota|Popis
----|----|----
Expr       | Textový řetězec | Strukturované dotazu výraz, který vybere podmnožinu index entity.
Atributy | Textový řetězec | Čárkami oddělený seznam atributů, které chcete zahrnout do odpovědi.
count      | Číslo (výchozí = 10) | Maximální počet výsledků vrátit.
Posun     | Číslo (výchozí = 0) | Index prvního výsledku vrátit.
Řadit podle |   Textový řetězec | Název atribut použitý k řazení výsledků, za nímž následuje volitelné řazení (výchozí = asc): "*%{attrname/*[: (asc&#124;desc)]".  Pokud není zadáno, budou vráceny výsledky, a tím snižují pravděpodobnosti přirozené protokolu.
timeout  | Číslo (výchozí = 1 000) | Časový limit v milisekundách. Se vrátí jenom výsledky počítaný předtím, než je časový limit uplynul.

Pomocí *počet* a *posun* parametry, může být velkého počtu výsledků získat přírůstkově více požadavků.
  
## <a name="response-json"></a>Odpověď (JSON)
JSONPath|Popis
----|----
$.expr | *Expr* parametr z požadavku.
$.entities | Pole 0 nebo více entit objekt odpovídající výrazu strukturovaných dotazů. 
$.aborted | Hodnota TRUE, pokud vypršel časový limit požadavku.

Každá entita obsahuje *logprob* hodnota a hodnoty požadované atributy.

## <a name="example"></a>Příklad:
V příkladu academic publikace následující požadavek předá výrazu strukturovaných dotazu (potenciálně z výstupu *interpretovat* požadavek) a načte několik atributů pro horní 2 odpovídající entity:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

Odpověď obsahuje horní části 2 ("count = 2") s největší pravděpodobností odpovídající entity.  Pro každou entitu jsou vráceny title, rok, jméno autora a atributy ID autora.  Všimněte si, že jak hodnoty strukturu složené atributu odpovídá způsob, jakým jsou uvedené v datovém souboru. 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
