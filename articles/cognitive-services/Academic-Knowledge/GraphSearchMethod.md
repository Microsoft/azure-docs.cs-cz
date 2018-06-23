---
title: Graf metodu Search v Academic Knowledge API | Microsoft Docs
description: Pomocí metody grafu, pro vyhledávání v Academic Knowledge API vrátit sady academic entit na základě způsobů určité grafů v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d811db117c934c0d41fbfea1220d241cc022e4a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342367"
---
# <a name="graph-search-method"></a>Metodu Search grafu

**Grafu vyhledávání** REST API se používá k vrácení sady academic entit na základě způsobů daným grafem.  Odpověď je grafu cesty, které splňují zadané uživatelem omezení. Se cesta ke grafu prokládaná řady grafu uzly a okrajů ve formě _v0, e0, v1, e1,..., vn_, kde _v0_ je počáteční uzel cesty.
<br>

**Koncový bod REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Parametry žádosti  
Název     | Hodnota | Povinné?  | Popis
-----------|-----------|---------|--------
**režim**       | Textový řetězec | Ano | Název režimu, který chcete použít. Hodnota je buď *json* nebo *lambda*.

Metodu search grafu musí být voláno prostřednictvím požadavku HTTP POST. Požadavek post by měla obsahovat záhlaví typu obsahu: **application/json**.

##### <a name="json-search"></a>Hledání JSON 

Pro *json* hledání textu POST je objekt JSON. Objekt JSON popisuje vzorek cesty pomocí zadaného uživatelem omezení (najdete v článku [specifikaci objekt JSON](JSONSearchSyntax.md) pro *json* vyhledávání).


##### <a name="lambda-search"></a>Hledání lambda

Pro *lambda* hledání textu POST je řetězec ve formátu prostého textu. Textu POST je řetězec dotazu LIKQ lambda, což je jediný příkaz jazyka C# (viz [specifikace řetězce dotazu](LambdaSearchSyntax.md) pro *lambda* vyhledávání). 

<br>
## <a name="response-json"></a>Odpověď (JSON)
Název | Popis
-------|-----   
**Výsledky** | Pole 0 nebo více entit, které odpovídají výrazu dotazu. Každá entita obsahuje hodnoty požadované atributy. Toto pole je k dispozici, pokud požadavek byl úspěšně zpracován.
**Chyba** | Stavové kódy HTTP. Toto pole je přítomen, pokud se požadavek nezdaří.
**Zpráva** | Chybová zpráva. Toto pole je přítomen, pokud se požadavek nezdaří.

Pokud dotaz nelze zpracovat v rámci _800 ms_, _časový limit_ bude vrácena chyba. 

<br>
#### <a name="example"></a>Příklad:

##### <a name="json-search"></a>Hledání JSON
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Pro *json* hledat, pokud chcete získat tyto dokumenty, jejichž názvy obsahovat "graf modul" a zapsaných správcem "bin shao", lze zadat dotaz následujícím způsobem.

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

Výstup tohoto dotazu je pole cesty grafu. Pole objektů uzlu odpovídající uzly zadaný v cestě dotazu se cesta ke grafu. Tyto objekty uzlu mají alespoň jednu vlastnost *CellID*, která reprezentuje ID entity. Ostatní vlastnosti můžete načíst tak, že zadáte názvy vlastností, které prostřednictvím operátor select z [ *objekt akce Traversal*](JSONSearchSyntax.md).

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

##### <a name="lambda-search"></a>Hledání lambda 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
Pro *lambda* hledání, pokud chcete získat ID daného dokumentu, Autor jsme můžete napsat dotaz stejný, jako je následující.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

Výstup *lambda* vyhledávací dotaz je také pole grafu cesty:

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

Graf schématu je užitečné pro zápis grafu vyhledávací dotazy. Zobrazí se na následujícím obrázku.

![Microsoft Academic Graph schématu](./Images/AcademicGraphSchema.png)
