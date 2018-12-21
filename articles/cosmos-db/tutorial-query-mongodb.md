---
title: Jak provádět dotazy na data ze služby Azure Cosmos DB pro rozhraní MongoDB API
description: Zjistěte, jak dotazovat pomocí rozhraní Azure Cosmos DB pro rozhraní API MongoDB.
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/29/2018
ms.openlocfilehash: bcfbb0115274c8b0d330426a454530dcd7c170ab
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724653"
---
# <a name="tutorial-query-data-using-azure-cosmos-db-for-mongodb-api"></a>Kurz: Dotazování dat pomocí služby Azure Cosmos DB pro rozhraní MongoDB API

Rozhraní [API pro MongoDB](mongodb-introduction.md) služby Azure Cosmos DB podporuje [dotazy prostředí MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * QQuerying dat uložených v databázi Cosmos přes MongoDB shell

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
> * Zjistili jste, jak provádět dotazy pomocí služby Cosmos DB nativní podporu pro rozhraní MongoDB API

Teď můžete pokračovat k dalšímu kurzu, kde se dozvíte, jak globálně distribuovat data.

> [!div class="nextstepaction"]
> [Globální distribuce dat](tutorial-global-distribution-sql-api.md)

