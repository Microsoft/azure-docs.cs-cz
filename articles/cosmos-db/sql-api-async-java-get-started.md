---
title: 'Kurz: Vytvoření aplikace Java pomocí sady Java SDK asynchronní ke správě účtu rozhraní SQL API ve službě Azure Cosmos DB'
description: V tomto kurzu se dozvíte, jak ukládat a přístup k datům v rámci účtu rozhraní SQL API ve službě Azure Cosmos DB s použitím aplikace Async Javu.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 80146c7ec7b496b33bede8172d9945c78b26511e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039023"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Kurz: Vytvoření aplikace Java pomocí sady Java SDK asynchronní ke správě dat uložených v účtu rozhraní SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (preview)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET core (Preview)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Jako vývojář můžete mít aplikace, které používají dat dokumentů typu NoSQL. Účet rozhraní SQL API ve službě Azure Cosmos DB můžete použít k ukládání a přístup k těmto datům dokumentu. V tomto kurzu se dozvíte, jak vytvářet aplikace v Javě pomocí sady Java SDK asynchronní k ukládání a správě dat dokumentu. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření a připojení k účtu Azure Cosmos
> * Konfigurace řešení
> * Vytvoření kolekce
> * Vytvoření dokumentů JSON
> * Dotazování na kolekci

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte následující prostředky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads)

* [Java Development Kit (JDK) 8+](https://aka.ms/azure-jdks).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Vytvoření účtu Azure Cosmos pomocí následujících kroků:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Naklonujte úložiště GitHub

Naklonujte úložiště GitHub pro projekt [Začínáme s Azure Cosmos DB a Javou](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Z místního adresáře, spusťte následující příkaz pro načtení ukázkový projekt místně.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

Adresář obsahuje `pom.xml` souboru a `src/main/java/com/microsoft/azure/cosmosdb/sample` složce obsahující zdrojový kód v Javě, včetně `Main.java`. Projekt obsahuje kód potřebný k provedení operací s Azure Cosmos DB, jako je vytváření dokumentů a dotazování dat v rámci kolekce. `pom.xml` Soubor obsahuje závislost na [Azure Cosmos DB Java SDK v nástroji Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Připojení k účtu Azure Cosmos

Dále přejděte zpět na [Azure Portal](https://portal.azure.com) a získejte koncový bod a primární hlavní klíč. Koncový bod a primární klíč služby Azure Cosmos DB jsou potřeba k tomu, aby aplikace věděla, kam se má připojit, a aby služba Azure Cosmos DB důvěřovala připojení aplikace. Soubor `AccountSettings.java` uchovává hodnoty primárního klíče a identifikátoru URI. 

Na webu Azure Portal, přejděte k vašemu účtu Azure Cosmos a potom klikněte na tlačítko **klíče**. Zkopírujte hodnoty identifikátoru URI a PRIMÁRNÍHO KLÍČE z webu Azure Portal a vložte je do souboru `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Snímek obrazovky se získáním klíčů z portálu][keys]

## <a name="initialize-the-client-object"></a>Inicializace objektu klienta

Inicializace objektu klienta pomocí hostitele identifikátoru URI a primární hodnoty klíče, které jsou definovány v souboru "AccountSettings.java".

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Vytvoření databáze

Vytvoření databáze Azure Cosmos DB s použitím `createDatabaseIfNotExists()` metoda třídy DocumentClient. Databáze je logický kontejner úložiště dokumentů JSON rozděleného mezi kolekcemi.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>Vytvoření kolekce

Můžete vytvořit kolekci pomocí `createDocumentCollectionIfNotExists()` metoda třídy DocumentClient. Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace.

> [!WARNING]
> **createCollection** vytvoří novou kolekci s vyhrazenou propustností, za kterou se hradí poplatky. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>Vytvoření dokumentů JSON

Vytvoření dokumentu pomocí metody createDocument třídy DocumentClient. Dokumenty představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete vložit jeden nebo více dokumentů. Soubor "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" definuje řady dokumentů JSON. 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>Dotazování prostředků Azure Cosmos DB

Azure Cosmos DB podporuje bohaté dotazy na dokumenty JSON uložené v každé z kolekcí. Následující ukázkový kód ukazuje dotazování na dokumenty ve službě Azure Cosmos DB pomocí syntaxe SQL `queryDocuments` metody.

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>Spuštění aplikace konzoly v jazyce Java

Ke spuštění aplikace z konzoly, přejděte do složky projektu a zkompilujte pomocí nástroje Maven:

```bash
mvn package
```

Spuštěním příkazu `mvn package` se z nástroje Maven stáhne nejnovější verze knihovny Azure Cosmos DB a vytvoří se soubor `GetStarted-0.0.1-SNAPSHOT.jar`. Potom spusťte aplikaci následujícím příkazem:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Právě jste dokončili tento kurz NoSQL a máte funkční konzolovou aplikaci Java.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste už nepotřebujete, můžete odstranit skupinu prostředků, účet Azure Cosmos a všechny související prostředky. Uděláte to tak, vyberte skupinu prostředků pro virtuální počítač, vyberte **odstranit**a potom ověřte název skupiny prostředků pro odstranění.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak vytvářet aplikace v Javě pomocí sady Java SDK asynchronní ke správě dat rozhraní SQL API ve službě Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Vytvoření konzolové aplikace Node.js s využitím sady JavaScript SDK a služby Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
