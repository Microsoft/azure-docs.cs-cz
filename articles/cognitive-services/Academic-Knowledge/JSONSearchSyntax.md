---
title: Syntaxe vyhledávání JSON v Academic Knowledge API | Microsoft Docs
description: Další informace o syntaxe vyhledávání JSON, které můžete použít v Academic Knowledge API v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a4b9cf535dae60258d71c43bba6f9eec1444bd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342359"
---
# <a name="json-search-syntax"></a>Syntaxe vyhledávání JSON

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

Názvy uzlů byste v cestě dotazu (_v0 v1,..._ ) slouží jako identifikátory uzlu, které mohou odkazovat v objektu dotazu. názvy okraj (_e0, e1,..._ ) v cestě představují typy odpovídající okraje. Můžeme použít hvězdičku _*_ jako název uzlu nebo Microsoft edge (s výjimkou počáteční uzel, který musí být poskytnut) deklarovat, nejsou žádná omezení na takový prvek. Například cestu dotazu `/v0/*/v1/e1/*/` načte cesty z grafu bez omezení typ okraj _(v0 v1)_. Mezitím dotaz nemá žádné omezení na cílové (poslední uzel) cesty buď.

Pokud cesta obsahuje pouze jeden uzel, vyslovení _v0_, jednoduše dotaz vrátí všechny entity, které odpovídají omezení. Objekt omezení u počáteční uzel se nazývá *od objektu dotazu*, jehož není určeno následujícím způsobem.

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

Pokud cesta obsahuje více než jen počáteční uzel, provede procesor dotazů graf traversal následující vzor zadaná cesta. Při doručení na uzlu, zadán uživatel traversal akce, které se spustí, to znamená, zda se zastaví na aktuálním uzlu a vrátit nebo pokračovat v průzkumu grafu. Pokud je zadána žádná akce traversal, výchozí akce se provedou. Pro zprostředkující uzel je výchozí akci pokračovat v průzkumu grafu. Pro poslední uzel, cesty je výchozí akci zastavit a vrátit. Omezení objektu, který určuje traversal akce je volána *objekt akce Traversal*. Jeho specifikaci je dán následujícím způsobem:

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

Tělo POST *json* vyhledávací dotaz by měl obsahovat alespoň *cesta* vzor. Objekty křížovou akce jsou volitelné. Tady jsou dva příklady.

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

