---
title: Dotazování dat pomocí služby Azure Cosmos DB přes rozhraní API pro MongoDB
description: Zjistěte, jak k dotazování dat pomocí služby Azure Cosmos DB přes rozhraní API pro MongoDB.
services: cosmos-db
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/26/2018
ms.openlocfilehash: b2bfc48fa0ba8aab0bbdf19e1b127155633e2271
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793202"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Dotazování dat pomocí rozhraní API služby Azure Cosmos DB pro MongoDB

[Rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-introduction.md) podporuje [MongoDB dotazy](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Dotazování na data uložená v databázi Cosmos přes MongoDB shell

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
## <a id="examplequery1"></a>Příklad dotazu 1 

S použitím výše uvedeného dokumentu family (rodina) vrátí následující dotaz dokumenty, jejichž pole ID odpovídá `WakefieldFamily`.

**Dotaz**
    
    db.families.find({ id: "WakefieldFamily"})

**Results**

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

## <a id="examplequery2"></a>Příklad dotazu 2 

Další dotaz vrátí všechny děti v rodině. 

**Dotaz**
    
    db.families.find( { id: "WakefieldFamily" }, { children: true } )

**Results**

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


## <a id="examplequery3"></a>Příklad dotazu 3 

Další dotaz vrátí všechny zaregistrované rodiny. 

**Dotaz**
    
    db.families.find( { "isRegistered" : true })
**Výsledky** Nevrátí se žádný dokument. 

## <a id="examplequery4"></a>Příklad dotazu 4

Další dotaz vrátí všechny nezaregistrované rodiny. 

**Dotaz**
    
    db.families.find( { "isRegistered" : false })
**Results**

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

## <a id="examplequery5"></a>Příklad dotazu 5

Další dotaz vrátí všechny nezaregistrované rodiny ve státě NY. 

**Dotaz**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**Results**

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


## <a id="examplequery6"></a>Příklad dotazu 6

Další dotaz vrátí všechny rodiny s dětmi v 8. ročníku.

**Dotaz**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**Results**

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

## <a id="examplequery7"></a>Příklad dotazu 7

Další dotaz vrátí všechny rodiny s polem children (děti) velikosti 3.

**Dotaz**
  
      db.Family.find( {children: { $size:3} } )

**Results**

Nevrátí se žádné výsledky, protože v žádné rodině není více než dvě děti. Tento dotaz bude úspěšný pouze v případě, že parametr bude 2, a pak vrátí celý dokument.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Zjistili jste, jak provádět dotazy pomocí rozhraní API Cosmos DB pro MongoDB

Teď můžete pokračovat k dalšímu kurzu, kde se dozvíte, jak globálně distribuovat data.

> [!div class="nextstepaction"]
> [Globální distribuce dat](tutorial-global-distribution-sql-api.md)

