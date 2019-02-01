---
title: Pomocí rozhraní API služby Azure Cosmos DB pro MongoDB můžete vytvářet aplikace v Node.js
description: Kurz, kterého se vytváří online databáze pomocí služby Azure Cosmos DB pro rozhraní API pro MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 28ee64f70cd281a2563a855fb1fca91f229ec7bd
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507514"
---
# <a name="build-an-app-using-nodejs-and-azure-cosmos-dbs-api-for-mongodb"></a>Vytváření aplikací pomocí Node.js a Azure Cosmos DB: rozhraní API pro MongoDB 
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js pro MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Tento příklad ukazuje, jak sestavit aplikaci konzoly pomocí Node.js a Azure Cosmos DB: rozhraní API pro MongoDB.

Chcete-li použít tento příklad, musíte:

* [Vytvoření](create-mongodb-dotnet.md#create-account) Cosmos účet nakonfigurovaný k používání služby Azure Cosmos DB API pro MongoDB.
* Načíst vaše [připojovací řetězec](connect-mongodb-account.md) informace.

## <a name="create-the-app"></a>Vytvoření aplikace

1. Vytvořte soubor *app.js* a zkopírujte a vložte kód níže.

    ```javascript
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
    
    **Volitelné**: Pokud používáte **ovladač MongoDB Node.js 2.2**, vyměňte následující fragment kódu:

    Původní:

    ```javascript
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

    ```javascript
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
    > Příklad: Heslo *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv ==* kóduje do *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv 3D % 3D*
    >
    > **Ovladač MongoDB Node.js 2.2** nevyžaduje kódování speciálních znaků v hesle Cosmos DB.
    >
    >
   
    ```javascript
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Otevřete svůj oblíbený terminál, spusťte příkaz **npm install mongodb --save** a pak spusťte aplikaci se souborem **node app.js**.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [použití studia 3T](mongodb-mongochef.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.
- Zjistěte, jak [použít Robo 3T](mongodb-robomongo.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.
- Prozkoumejte MongoDB [ukázky](mongodb-samples.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.
