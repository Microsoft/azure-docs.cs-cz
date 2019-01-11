---
title: 'Kurz k NoSQL: Rozhraní SQL API pro Azure Cosmos DB Java SDK'
description: Kurz k NoSQL, v rámci kterého se vytvoří online databáze a aplikace konzoly Java pomocí rozhraní SQL API pro Azure Cosmos DB. Azure SQL je databáze NoSQL pro JSON.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: sngun
ms.openlocfilehash: 7b59ab5da89d7ab99560a777f5a685f8b33e31dc
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201172"
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>Kurz k NoSQL: Vytvoření konzolové aplikace SQL API Java

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (preview)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET core (Preview)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Vítejte v kurzu k NoSQL pro rozhraní SQL API sady Azure Cosmos DB Java SDK! Až projdete tímto kurzem, budete mít konzolovou aplikaci, která vytváří prostředky Azure Cosmos DB a dotazuje se na ně.

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
* [Java Development Kit (JDK) 7+](https://aka.ms/azure-jdks)
* [Maven](https://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB
Vytvořme účet služby Azure Cosmos DB. Pokud už máte účet, který chcete použít, můžete přeskočit k části [Klonování projektu z GitHubu](#GitClone). Pokud používáte emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md) a přeskočte k části [Klonování projektu z GitHubu](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Krok 2: Klonování projektu z Githubu
Můžete začít naklonováním úložiště GitHub pro projekt [Začínáme se službou Azure Cosmos DB a Javou](https://github.com/Azure-Samples/documentdb-java-getting-started). Například spusťte z místního adresáře následující příkaz, který načte ukázkový projekt pro místní použití.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

Adresář obsahuje soubor `pom.xml` pro projekt a složku `src` obsahující zdrojový kód Java, včetně souboru `Program.java`, který ukazuje, jak s Azure Cosmos DB provádět jednoduché operace, jako je vytváření dokumentů a dotazování dat v kolekci. Soubor `pom.xml` obsahuje závislost na sadu [Azure Cosmos B Java SDK v nástroji Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Krok 3: Připojte se k účtu služby Azure Cosmos DB
Dále přejděte zpět na [Azure Portal](https://portal.azure.com) a získejte koncový bod a primární hlavní klíč. Koncový bod a primární klíč služby Azure Cosmos DB jsou potřeba k tomu, aby aplikace věděla, kam se má připojit, a aby služba Azure Cosmos DB důvěřovala připojení aplikace.

Na webu Azure Portal přejděte do účtu služby Azure Cosmos DB a klikněte na **Klíče**. Zkopírujte identifikátor URI z portálu a vložte ho do `https://FILLME.documents.azure.com` v souboru Program.java. Poté zkopírujte PRIMÁRNÍ KLÍČ z portálu a vložte ho do `FILLME`.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Snímek obrazovky webu Azure Portal, který se v kurzu NoSQL používá k vytvoření konzolové aplikace v jazyce Java. Ukazuje účet služby Azure Cosmos DB se zvýrazněným aktivním centrem, zvýrazněným tlačítkem KLÍČE v okně účtu služby Azure Cosmos DB a zvýrazněnými hodnotami URI, PRIMÁRNÍ KLÍČ a SEKUNDÁRNÍ KLÍČ v okně Klíče.][keys]

## <a name="step-4-create-a-database"></a>Krok 4: Vytvoření databáze
[Databázi](databases-containers-items.md#azure-cosmos-databases) Azure Cosmos DB je možné vytvořit pomocí metody [createDatabase](/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase) třídy **DocumentClient**. Databáze je logický kontejner úložiště dokumentů JSON rozděleného mezi kolekcemi.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Krok 5: Vytvoření kolekce
> [!WARNING]
> **createCollection** vytvoří novou kolekci s vyhrazenou propustností, za kterou se hradí poplatky. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Kolekce lze vytvořit pomocí [createCollection](/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) metodu **DocumentClient** třídy. Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Krok 6: Vytvoření dokumentů JSON
Dokument lze vytvořit pomocí [createDocument](/java/api/com.microsoft.azure.documentdb.documentclient.createdocument) metodu **DocumentClient** třídy. Dokumenty představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete vložit jeden nebo více dokumentů. Pokud již máte data, která chcete uložit do databáze, můžete použít [nástroj pro migraci dat](import-data.md) služby Azure Cosmos DB a importovat tato data do databáze.

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
Azure Cosmos DB podporuje bohaté [dotazy](how-to-sql-query.md) na dokumenty JSON uložené v každé z kolekcí.  Následující ukázkový kód ukazuje dotazování na dokumenty ve službě Azure Cosmos DB pomocí syntaxe SQL a metody [queryDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Krok 8: Nahrazení dokumentu JSON
Azure Cosmos DB podporuje aktualizaci dokumentů JSON pomocí metody [replaceDocument](/java/api/com.microsoft.azure.documentdb.documentclient.replacedocument).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Krok 9: Odstranění dokumentu JSON
Podobně Azure Cosmos DB podporuje odstraňování dokumentů JSON pomocí metody [deleteDocument](/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Krok 10: Odstranění databáze
Odstraněním vytvořené databáze dojde k odebrání databáze a všech jejích podřízených prostředků (kolekcí, dokumentů atd.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Krok 11: Spustit vaše Java všechno dohromady konzolové aplikace!
Chcete-li aplikaci spustit z konzoly, přejděte do složky projektu a proveďte kompilaci pomocí nástroje Maven:
    
    mvn package

Spuštěním příkazu `mvn package` se z nástroje Maven stáhne nejnovější verze knihovny Azure Cosmos DB a vytvoří se soubor `GetStarted-0.0.1-SNAPSHOT.jar`. Potom spusťte aplikaci následujícím příkazem:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Blahopřejeme! Dokončili jste tento kurz NoSQL a máte funkční konzolovou aplikaci jazyka Java!

## <a name="next-steps"></a>Další postup
* Chcete kurz vývoje webové aplikace v jazyce Java? Viz [Vytvoření webové aplikace pomocí Javy a služby Azure Cosmos DB](sql-api-java-application.md).
* Zjistěte, jak [monitorovat účet služby Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
