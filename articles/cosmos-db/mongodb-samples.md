---
title: Použití rozhraní MongoDB API k sestavení aplikace Azure Cosmos DB | Microsoft Docs
description: Kurz, ve kterém se vytvoří online databáze pomocí rozhraní Azure Cosmos DB API pro MongoDB.
keywords: mongodb examples
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 188b192cf9b86a2d28a578bbcec0d6b19a8cc5d0
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084898"
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Sestavení aplikace Azure Cosmos DB: API for MongoDB pomocí Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js pro MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Tento příklad ukazuje, jak sestavit konzolovou aplikaci Azure Cosmos DB: API for MongoDB pomocí Node.js.

Chcete-li použít tento příklad, musíte:

* [Vytvořit](create-mongodb-dotnet.md#create-account) účet Azure Cosmos DB: API for MongoDB.
* Načíst informace [připojovacího řetězce](connect-mongodb-account.md) MongoDB.

## <a name="create-the-app"></a>Vytvoření aplikace

1. Vytvořte soubor *app.js* a zkopírujte a vložte kód níže.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Volitelné:** Pokud používáte **ovladač MongoDB Node.js 2.2**, nahraďte následující fragment kódu:

    Původní:

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Měl by být nahrazen tímto:

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Upravte následující proměnné v souboru *app.js* pro nastavení vašeho účtu (informace o vyhledání vašeho [připojovacího řetězce](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > **Ovladač MongoDB Node.js 3.0** vyžaduje kódování speciálních znaků v hesle Cosmos DB. Zajistěte, aby byly znaky „=“ kódované jako %3D.
    >
    > Příklad: Heslo *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* se kóduje na *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*.
    >
    > **Ovladač MongoDB Node.js 2.2** nevyžaduje kódování speciálních znaků v hesle Cosmos DB.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Otevřete svůj oblíbený terminál, spusťte příkaz **npm install mongodb --save** a pak spusťte aplikaci se souborem **node app.js**.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [použít MongoChef](mongodb-mongochef.md) s vaším účtem Azure Cosmos DB: API for MongoDB.
