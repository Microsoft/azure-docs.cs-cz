---
title: Dotazování dat pomocí rozhraní API Azure Cosmos DB pro MongoDB
description: Naučte se, jak zadávat dotazy na data z rozhraní API Azure Cosmos DB MongoDB pomocí příkazů prostředí MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: f93ec39e7a2e3b5829c0d6205404c6c5c4af6189
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93074314"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Dotazování dat pomocí rozhraní API Azure Cosmos DB pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Rozhraní API Azure Cosmos DB pro MongoDB](mongodb-introduction.md) podporuje [dotazy MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Dotazování na data uložená ve vaší databázi Cosmos pomocí prostředí MongoDB

Můžete začít s použitím příkladů v tomto dokumentu a podívat se na video o [dotazování služby Azure Cosmos DB pomocí prostředí MongoDB Shell](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/).

## <a name="sample-document"></a>Ukázkový dokument

Dotazy v tomto článku využívají následující ukázkový dokument.

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
## <a name="example-query-1"></a><a id="examplequery1"></a>Příklad dotazu 1 

S použitím výše uvedeného dokumentu family (rodina) vrátí následující dotaz dokumenty, jejichž pole ID odpovídá `WakefieldFamily`.

**Dotaz**

```bash
db.families.find({ id: "WakefieldFamily"})
```

**Výsledky**

```json
{
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-2"></a><a id="examplequery2"></a>Příklad dotazu 2 

Další dotaz vrátí všechny děti v rodině. 

**Dotaz**

```bash 
db.families.find( { id: "WakefieldFamily" }, { children: true } )
``` 

**Výsledky**

```json
{
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
}
```

## <a name="example-query-3"></a><a id="examplequery3"></a>Příklad dotazu 3 

Další dotaz vrátí všechny zaregistrované rodiny. 

**Dotaz**

```bash
db.families.find( { "isRegistered" : true })
``` 

**Výsledky**

Nebude vrácen žádný dokument. 

## <a name="example-query-4"></a><a id="examplequery4"></a>Příklad dotazu 4

Další dotaz vrátí všechny nezaregistrované rodiny. 

**Dotaz**

```bash
db.families.find( { "isRegistered" : false })
``` 

**Výsledky**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-5"></a><a id="examplequery5"></a>Příklad dotazu 5

Další dotaz vrátí všechny nezaregistrované rodiny ve státě NY. 

**Dotaz**

```bash
db.families.find( { "isRegistered" : false, "address.state" : "NY" })
``` 

**Výsledky**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-6"></a><a id="examplequery6"></a>Příklad dotazu 6

Další dotaz vrátí všechny rodiny s dětmi v 8. ročníku.

**Dotaz**

```bash
db.families.find( { children : { $elemMatch: { grade : 8 }} } )
```

**Výsledky**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-7"></a><a id="examplequery7"></a>Příklad dotazu 7

Další dotaz vrátí všechny rodiny s polem children (děti) velikosti 3.

**Dotaz**

```bash
db.Family.find( {children: { $size:3} } )
```

**Výsledky**

Nevrátí se žádné výsledky, protože v žádné rodině není více než dvě děti. Tento dotaz bude úspěšný pouze v případě, že parametr bude 2, a pak vrátí celý dokument.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Seznámili jste se s dotazování pomocí rozhraní Cosmos DB API pro MongoDB.

Teď můžete pokračovat k dalšímu kurzu, kde se dozvíte, jak globálně distribuovat data.

> [!div class="nextstepaction"]
> [Globální distribuce dat](tutorial-global-distribution-sql-api.md)

