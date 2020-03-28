---
title: 'Kurz: Jak se dotazovat pomocí SQL v Azure Cosmos DB?'
description: 'Kurz: Naučte se dotazovat se pomocí dotazů SQL v Azure Cosmos DB pomocí hřiště pro dotazy thw'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: 7e83ed0f9e635ed24b7e6115eeaaa9057d422c69
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74870067"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Kurz: Dotazování služby Azure Cosmos DB pomocí rozhraní SQL API

Rozhraní [SQL API](documentdb-introduction.md) služby Azure Cosmos DB podporuje dotazování dokumentů pomocí jazyka SQL. Tento článek obsahuje ukázkový dokument a dva ukázkové dotazy SQL a jejich výsledky.

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Dotazování dat pomocí jazyka SQL

## <a name="sample-document"></a>Ukázkový dokument

Dotazy SQL v tomto článku využívají následující ukázkový dokument.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a name="where-can-i-run-sql-queries"></a>Kde můžu spouštět dotazy SQL?

Dotazy můžete spouštět pomocí Průzkumníka dat na webu Azure Portal, prostřednictvím [rozhraní REST API a sad SDK](sql-api-sdk-dotnet.md) a dokonce i pomocí [Query Playground](https://www.documentdb.com/sql/demo), kde se spouští dotazy na existující sadu ukázkových dat.

Další informace o dotazech SQL najdete tady:
* [Dotazy a syntaxe SQL](sql-query-getting-started.md)

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že máte účet a kolekci Azure Cosmos DB. Něco z toho nemáte? Dokončete [pětiminutový rychlý start](create-cosmosdb-resources-portal.md).

## <a name="example-query-1"></a>Příklad dotazu 1

S použitím výše uvedeného dokumentu family (rodina) vrátí následující dotaz SQL dokumenty, jejichž pole ID odpovídá `WakefieldFamily`. Vzhledem k tomu, že se jedná o příkaz `SELECT *`, výstupem dotazu bude celý dokument JSON:

**Dotazu**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Results**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="example-query-2"></a>Příklad dotazu 2

Další dotaz vrátí křestní jména všech dětí v rodině, jejíž ID odpovídá `WakefieldFamily`, seřazená podle ročníku.

**Dotazu**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'

**Results**

[ { "givenName": "Jesse" }, { "givenName": "Lisa" } ]


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Zjistili jste, jak provádět dotazy pomocí jazyka SQL.  

Teď můžete pokračovat k dalšímu kurzu, kde se dozvíte, jak globálně distribuovat data.

> [!div class="nextstepaction"]
> [Globální distribuce dat](tutorial-global-distribution-sql-api.md)

