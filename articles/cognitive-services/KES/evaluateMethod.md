---
title: Vyhodnocení metody – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak použít metodu vyhodnotit v znalostní báze zkoumání služby (KES) rozhraní API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: dcfa9bb7931cf3b682bacf722b67acd6d4a370c0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860724"
---
# <a name="evaluate-method"></a>vyhodnocení metody

*Vyhodnotit* metoda vyhodnotí a vrátí její výstup výrazu strukturovaných dotazů založené na datech indexu.

Výraz obvykle budou získány z odpovědí na metodu interpretaci.  Ale můžete také vytvářet – výrazy dotazů sami (viz [strukturovaných výrazu dotazu](Expressions.md)).  

## <a name="request"></a>Žádost 

`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

Název|Hodnota|Popis
----|----|----
expr       | Textový řetězec | Výraz strukturovaných dotazů, který vybere podmnožinu index entity.
Atributy | Textový řetězec | Čárkami oddělený seznam atributů, které chcete zahrnout do odpovědi.
count      | Číslo (výchozí = 10) | Maximální počet výsledků k vrácení.
Posun     | Číslo (výchozí = 0) | Index první výsledek vrátit.
Řadit podle |   Textový řetězec | Název atributu použitého k řazení výsledků, za nímž následuje nepovinný řazení (výchozí = asc): "*%{attrname/*[: (asc&#124;desc)]".  Pokud není zadán, jsou výsledky vrácené snižuje pravděpodobnost přirozený protokolu.
timeout  | Číslo (výchozí = 1000) | Časový limit v milisekundách. Se vrátí pouze výsledky vypočítán dříve, než vypršel časový limit.

Použití *počet* a *posun* parametry, může být velký počet výsledků získávají postupně prostřednictvím více požadavků.
  
## <a name="response-json"></a>Odpověď (JSON)
JSONPath|Popis
----|----
$.expr | *výraz* parametr z požadavku.
$.entities | Pole 0 nebo více entit objektu odpovídající výraz strukturovaných dotazů. 
$.aborted | True, pokud vypršel časový limit žádosti.

Každá entita obsahuje *logprob* hodnoty a hodnoty požadovaných atributů.

## <a name="example"></a>Příklad:
V příkladu academic publikace následující požadavek předá výrazu strukturovaných dotazů (potenciálně z výstupu *interpretovat* žádosti) a načte několik atributů pro začátek 2 odpovídající entity:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

Odpověď obsahuje 2 největší ("počet = 2") pravděpodobně odpovídající entity.  Pro každou entitu jsou vráceny title, rok, jméno autora a atributy ID autora.  Všimněte si, jak Struktura složeného hodnoty atributů odpovídá způsob, jakým jsou určené v datovém souboru. 

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
