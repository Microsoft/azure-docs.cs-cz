---
title: 'Kurzu NoSQL: SQL API týkající se Azure Cosmos DB Java SDK | Microsoft Docs'
description: Kurz NoSQL, která vytváří online databáze a Konzolová aplikace Java pomocí rozhraní SQL API pro Azure Cosmos DB. Azure SQL je databáze NoSQL pro JSON.
keywords: nosql tutorial, online database, java console application
services: cosmos-db
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/22/2017
ms.author: arramac
ms.openlocfilehash: 5052c3cdeabd5001c6d0144dc77401a9495ba887
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>Kurzu NoSQL: vytvoření konzolové aplikace SQL API Java
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js pro MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Vítejte v kurzu NoSQL pro rozhraní SQL API pro Azure Cosmos DB Java SDK! Až projdete tímto kurzem, budete mít konzolovou aplikaci, která vytváří prostředky Azure Cosmos DB a dotazuje se na ně.

Kurz zahrnuje:

* Vytvoření účtu služby Azure Cosmos DB a připojení k němu
* Konfigurace řešení v nástroji Visual Studio
* Vytvoření online databáze
* Vytvoření kolekce
* Vytvoření dokumentů JSON
* Dotazování na kolekci
* Vytvoření dokumentů JSON
* Dotazování na kolekci
* Nahrazení dokumentu
* Odstranění dokumentu
* Odstranění databáze

Můžeme začít!

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že máte následující:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads)
* [Java Development Kit (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB
Vytvořme účet služby Azure Cosmos DB. Pokud už máte účet, který chcete použít, můžete přeskočit k části [Klonování projektu z GitHubu](#GitClone). Pokud používáte emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md) a přeskočte k části [Klonování projektu z GitHubu](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Krok 2: Klonování projektu z GitHubu
Můžete začít naklonováním úložiště GitHub pro projekt [Začínáme se službou Azure Cosmos DB a Javou](https://github.com/Azure-Samples/documentdb-java-getting-started). Například spusťte z místního adresáře následující příkaz, který načte ukázkový projekt pro místní použití.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

Obsahuje adresáři `pom.xml` pro projekt a `src` složku obsahující Java zdrojového kódu včetně `Program.java` které ukazuje, jak provádějí jednoduché operace s Azure DB Cosmos jako je vytváření dokumentů a dotazování na data v rámci kolekce . `pom.xml` Obsahuje závislost na [Azure Cosmos DB Java SDK na Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Krok 3: Připojení k účtu služby Azure Cosmos DB
Dále přejděte zpět na [Azure Portal](https://portal.azure.com) a získejte koncový bod a primární hlavní klíč. Koncový bod a primární klíč služby Azure Cosmos DB jsou potřeba k tomu, aby aplikace věděla, kam se má připojit, a aby služba Azure Cosmos DB důvěřovala připojení aplikace.

Na webu Azure Portal přejděte do účtu služby Azure Cosmos DB a klikněte na **Klíče**. Zkopírujte identifikátor URI z portálu a vložte ho do `https://FILLME.documents.azure.com` v souboru Program.java. Poté zkopírujte PRIMÁRNÍ KLÍČ z portálu a vložte ho do `FILLME`.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Snímek obrazovky webu Azure Portal, který se v kurzu NoSQL používá k vytvoření konzolové aplikace v jazyce Java. Ukazuje účet služby Azure Cosmos DB se zvýrazněným aktivním centrem, zvýrazněným tlačítkem KLÍČE v okně účtu služby Azure Cosmos DB a zvýrazněnými hodnotami URI, PRIMÁRNÍ KLÍČ a SEKUNDÁRNÍ KLÍČ v okně Klíče.][keys]

## <a name="step-4-create-a-database"></a>Krok 4: Vytvoření databáze
[Databázi](sql-api-resources.md#databases) Azure Cosmos DB je možné vytvořit pomocí metody [createDatabase](/java/api/com.microsoft.azure.documentdb._document_client.createdatabase) třídy **DocumentClient**. Databáze je logický kontejner úložiště dokumentů JSON rozděleného mezi kolekcemi.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Krok 5: Vytvoření kolekce
> [!WARNING]
> **createCollection** vytvoří novou kolekci s vyhrazenou propustností, za kterou se hradí poplatky. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

[Kolekci](sql-api-resources.md#collections) je možné vytvořit pomocí metody [createCollection](/java/api/com.microsoft.azure.documentdb._document_client.createcollection) třídy **DocumentClient**. Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Krok 6: Vytvoření dokumentů JSON
[Dokument](sql-api-resources.md#documents) je možné vytvořit pomocí metody [createDocument](/java/api/com.microsoft.azure.documentdb._document_client.createdocument) třídy **DocumentClient**. Dokumenty představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete vložit jeden nebo více dokumentů. Pokud již máte data, která chcete uložit do databáze, můžete použít Azure Cosmos DB [nástroj pro migraci dat](import-data.md) pro import dat do databáze.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagram ilustrující hierarchický vztah mezi účtem, online databází, kolekcí a dokumenty používanými v kurzu NoSQL k vytvoření konzolové aplikace v jazyce Java](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Krok 7: Dotazování prostředků Azure Cosmos DB
Azure Cosmos DB podporuje bohaté [dotazy](sql-api-sql-query.md) na dokumenty JSON uložené v každé z kolekcí.  Následující ukázkový kód ukazuje dotazování na dokumenty ve službě Azure Cosmos DB pomocí syntaxe SQL a metody [queryDocuments](/java/api/com.microsoft.azure.documentdb._document_client.querydocuments).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Krok 8: Nahrazení dokumentu JSON
Azure Cosmos DB podporuje aktualizaci dokumentů JSON pomocí metody [replaceDocument](/java/api/com.microsoft.azure.documentdb._document_client.replacedocument).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Krok 9: Odstranění dokumentu JSON
Podobně Azure Cosmos DB podporuje odstraňování dokumentů JSON pomocí metody [deleteDocument](/java/api/com.microsoft.azure.documentdb._document_client.deletedocument).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Krok 10: Odstranění databáze
Odstraněním vytvořené databáze dojde k odebrání databáze a všech jejích podřízených prostředků (kolekcí, dokumentů atd.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Krok 11: Spuštění celé konzolové aplikace jazyka Java!
Ke spuštění aplikace z konzoly, přejděte do složky projektu a zkompilovat pomocí nástroje Maven:
    
    mvn package

Spuštěním příkazu `mvn package` se z nástroje Maven stáhne nejnovější verze knihovny Azure Cosmos DB a vytvoří se soubor `GetStarted-0.0.1-SNAPSHOT.jar`. Potom spusťte aplikaci následujícím příkazem:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Blahopřejeme! Dokončili jste tento kurz NoSQL a máte funkční konzolovou aplikaci jazyka Java!

## <a name="next-steps"></a>Další postup
* Chcete kurz vývoje webové aplikace v jazyce Java? Viz [Vytvoření webové aplikace pomocí Javy a služby Azure Cosmos DB](sql-api-java-application.md).
* Zjistěte, jak [monitorovat účet služby Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
