---
title: Metoda hledání – Academic Knowledge API Graph
titlesuffix: Azure Cognitive Services
description: Pomocí hledání v grafu metody v rozhraní Academic Knowledge API vrátit sadu akademické entity, které na základě způsobů konkrétní graf.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f6c2fbe5daeb114d6a5ea77c9823f1fa5bfe8425
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864464"
---
# <a name="graph-search-method"></a>Metoda prohledávání grafů

**Hledání v grafu** rozhraní REST API se používá k vrácení sady akademické entity, které na základě vzorců daným grafem.  Odpověď je sada cest grafu nesplňujete omezení zadané uživatelem. Cesta grafu je prokládané pořadí grafu uzlů a hran v podobě _v0, e0, v1, e1,..., vn_, kde _v0_ je počáteční uzel cesty.
<br>

**Koncový bod REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Parametry žádosti  
Název     | Hodnota | Povinné?  | Popis
-----------|-----------|---------|--------
**Režim**       | Textový řetězec | Ano | Název režimu, který chcete použít. Hodnota je buď *json* nebo *lambda*.

Metoda prohledávání grafů musí volat prostřednictvím požadavku HTTP POST. Požadavek post by měl obsahovat záhlaví typu obsahu: **application/json**.

##### <a name="json-search"></a>Hledání JSON 

Pro *json* hledání textu POST je objekt JSON. Objekt JSON popisuje vzor cesty pomocí zadaného uživatelem omezení (najdete v článku [specifikace objektu JSON](JSONSearchSyntax.md) pro *json* vyhledávání).


##### <a name="lambda-search"></a>Hledání výrazu lambda

Pro *lambda* hledání textu POST je řetězec ve formátu prostého textu. Tělo POST je řetězec LIKQ lambda dotazu, což je jeden příkaz jazyka C# (najdete v článku [specifikace řetězec dotazu](LambdaSearchSyntax.md) pro *lambda* vyhledávání). 

<br>
## <a name="response-json"></a>Odpověď (JSON)
Název | Popis
-------|-----   
**výsledky** | Pole 0 nebo více entit, které odpovídají výrazu dotazu. Každá entita obsahuje hodnoty požadovaných atributů. Toto pole je k dispozici v případě, že byla žádost úspěšně zpracována.
**error** | Stavové kódy HTTP. Toto pole je k dispozici, pokud požadavek selže.
**Zpráva** | Chybová zpráva. Toto pole je k dispozici, pokud požadavek selže.

Pokud dotaz nelze zpracovat v rámci _800 ms_, _vypršení časového limitu_ se vrátí chyba. 

<br>
#### <a name="example"></a>Příklad:

##### <a name="json-search"></a>Hledání JSON
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Pro *json* hledání, pokud chcete získat tyto dokumenty, jejichž názvy obsahují "grafu modul" a zapisuje "bin shao", lze zadat dotaz následujícím způsobem.

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

Výstup dotazu je pole cest grafu. Cesta grafu je pole objektů uzlu odpovídající uzly zadaný v cestě k dotazu. Tyto uzly objekty mají alespoň jednu vlastnost *CellID*, která představuje ID entity. Další vlastnosti můžete načíst tak, že zadáte názvy vlastností, které prostřednictvím vyberte operátor [ *objektu akce procházení*](JSONSearchSyntax.md).

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>Hledání výrazu lambda 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
Pro *lambda* vyhledávání, pokud chcete získat ID daného papíru, Autor jsme můžete napsat dotaz podobný následujícímu.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

Výstup *lambda* vyhledávacího dotazu je také pole cest grafu:

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>Schéma grafu

Schéma grafu je užitečné při vytváření grafu vyhledávací dotazy. Je znázorněno na následujícím obrázku.

![Schéma Microsoft Academic Graph](./Images/AcademicGraphSchema.png)
