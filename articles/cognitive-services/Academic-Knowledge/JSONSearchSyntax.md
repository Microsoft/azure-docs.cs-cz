---
title: Syntaxe prohledávání JSON – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další informace o syntaxi vyhledávání souboru JSON, která vám pomůže v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 5ece028f89ad9e93840211383db97a5d8a80069a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900406"
---
# <a name="json-search-syntax"></a>Syntaxe prohledávání JSON

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

Názvy uzlů v cestě dotazu (_v0 v1,..._ ) bude sloužit jako identifikátory uzlů, které lze odkazovat v objektu dotazu. názvy edge (_e0 e1,..._ ) v cestě představují typy odpovídající hran. Můžeme použít hvězdičku _*_ jako uzlů nebo hran název (s výjimkou počáteční uzel, který se musí předávat) Chcete-li deklarovat, které nejsou bez omezení na takový prvek. Například cestu dotazu `/v0/*/v1/e1/*/` načte cesty z grafu bez omezení typu na hraničních zařízeních _(v0 v1)_. Mezitím dotaz nemá omezení v cílovém umístění (poslední uzel) cesta buď.

Pokud cesta obsahuje pouze jeden uzel, Řekněme, že _v0_, jednoduše dotaz vrátí všechny entity, které vyhovují omezení. Omezení objekt použitý pro počáteční uzel, se nazývá *od objektu dotazu*, jejichž specifikace dostane následujícím způsobem.

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

Pokud cesta obsahuje více než jen počáteční uzel, provede procesor dotazů grafu procházení následující vzor zadané cesty. Při příchodu uzel, akce procházení zadané uživatelem se aktivuje, to znamená, zda se zastaví na aktuální uzel a vrátit, nebo pokračovat v průzkumu grafu. Pokud není zadána žádná akce procházení, provedou se výchozí akce. Výchozí akce pro zprostředkující uzel, je dále prozkoumávat grafu. Pro poslední uzel cesty je výchozí akci k zastavení a vrácení. Je volána zadaných objektů omezení, která určuje akce procházení *objektu akce procházení*. Jeho specifikaci se vypočte takto:

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

Tělo POST *json* vyhledávací dotaz by měl obsahovat alespoň *cesta* vzor. Procházet akce objekty jsou volitelné. Tady jsou dva příklady.

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

