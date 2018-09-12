---
title: 'Azure Cosmos DB: Jak provádět dotazy pomocí rozhraní MongoDB API? | Microsoft Docs'
description: Zjistěte, jak provádět dotazy pomocí rozhraní MongoDB API pro službu Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: efb59a73b3c9b0ab06fae2e7b4fe5b97d85249eb
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052801"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-mongodb-api"></a>Kurz: Dotazování služby Azure Cosmos DB pomocí rozhraní MongoDB API

Rozhraní [API pro MongoDB](mongodb-introduction.md) služby Azure Cosmos DB podporuje [dotazy prostředí MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Dotazování dat pomocí MongoDB

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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Zjistili jste, jak provádět dotazy pomocí MongoDB. 

Teď můžete pokračovat k dalšímu kurzu, kde se dozvíte, jak globálně distribuovat data.

> [!div class="nextstepaction"]
> [Globální distribuce dat](tutorial-global-distribution-sql-api.md)

