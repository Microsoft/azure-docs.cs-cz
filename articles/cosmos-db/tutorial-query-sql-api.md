---
title: Jak provádět dotazy pomocí jazyka SQL ve službě Azure Cosmos DB? | Dokumenty Microsoft
description: Zjistěte, jak provádět dotazy pomocí jazyka SQL ve službě Azure Cosmos DB.
services: cosmos-db
author: rafats
manager: kfile
editor: ''
tags: ''
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: rafats
ms.openlocfilehash: 5f5a98f0f28eba499b7ea3fa76944c21cf8bf8db
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958864"
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
* [Dotazy a syntaxe SQL](sql-api-sql-query.md)

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že máte účet a kolekci Azure Cosmos DB. Něco z toho nemáte? Dokončete [pětiminutový rychlý start](create-mongodb-nodejs.md).

## <a name="example-query-1"></a>Příklad dotazu 1

S použitím výše uvedeného dokumentu family (rodina) vrátí následující dotaz SQL dokumenty, jejichž pole ID odpovídá `WakefieldFamily`. Vzhledem k tomu, že se jedná o příkaz `SELECT *`, výstupem dotazu bude celý dokument JSON:

**Dotaz**

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

**Dotaz**

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

