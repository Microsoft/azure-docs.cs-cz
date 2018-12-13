---
title: 'Azure Cosmos DB: Sestavení aplikace konzoly v jazyce Java s rozhraním API MongoDB'
description: Představuje ukázku kódu Java, kterou můžete použít k připojení a dotazování do rozhraní API MongoDB služby Azure Cosmos DB.
services: cosmos-db
author: slyons
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quick start connect, mvc
ms.devlang: java
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: sclyon
ms.openlocfilehash: 30e87ba14c6754fa39269f3afac318a02cf99a2c
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162014"
---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-java-and-the-azure-portal"></a>Azure Cosmos DB: Sestavení aplikace konzoly rozhraní MongoDB API pomocí Javy a webu Azure portal

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos. 

Tento rychlý start popisuje způsob vytvoření účtu rozhraní [MongoDB API](mongodb-introduction.md) služby Azure Cosmos DB, databáze dokumentů a kolekce pomocí webu Azure Portal. Potom sestavíte a nasadíte aplikaci konzoly založenou na [ovladači Java MongoDB](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Požadavky

Než budete moct tuto ukázku spustit, je potřeba splnit následující požadavky:
* JDK 1.7+ (pokud nemáte JDK, spusťte `apt-get install default-jdk`)
* Maven (pokud nemáte Maven, spusťte `apt-get install maven`)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Přidání kolekce

Novou databázi pojmenujte **db** a novou kolekci **coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci rozhraní API MongoDB z Githubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a potom příkazový řádek zavřete.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Pak otevřete kód v oblíbeném editoru. 

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string). 

Všechny následující fragmenty kódu pocházejí ze souboru Program.java.

* Inicializuje se DocumentClient.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* Vytvoří se nová databáze a kolekce.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* Pomocí metody `MongoCollection.insertOne` se vloží některé dokumenty.

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* Pomocí metody `MongoCollection.find` se provedou některé dotazy.

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace.

1. V účtu vyberte možnost **Rychlý start**, vyberte jazyk Java a potom zkopírujte připojovací řetězec do schránky.

2. Otevřete soubor `Program.java` a nahraďte argument konstruktoru MongoClientURI připojovacím řetězcem. Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Spuštění aplikace konzoly

1. Spusťte v terminálu `mvn package`, aby se nainstalovaly požadované moduly NPM.

2. Spuštění v terminálu `mvn exec:java -D exec.mainClass=GetStarted.Program`, aby se spustila aplikace Java.

Teď můžete provádět dotazy a úpravy a pracovat s těmito novými daty v prostředí použít [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md).

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu služby Azure Cosmos DB, vytvoření kolekce pomocí Průzkumníka dat a spuštění aplikace konzoly. Teď můžete do účtu Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat MongoDB do databáze Azure Cosmos](mongodb-migrate.md)


