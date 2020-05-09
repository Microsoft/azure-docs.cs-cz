---
title: 'Kurz: Vytvoření aplikace Java pomocí Azure Cosmos DB Async Java SDK pro správu dat v účtu SQL API'
description: V tomto kurzu se dozvíte, jak uložit a přistupovat k datům v rámci účtu rozhraní SQL API v Azure Cosmos DB pomocí asynchronní aplikace Java.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: eb30b8b210d34205620dec8efe059ef50c45f8e4
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858666"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Kurz: Vytvoření aplikace Java s asynchronní sadou Java SDK pro správu dat uložených v účtu rozhraní SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Jako vývojář můžete mít aplikace, které používají data dokumentů NoSQL. K ukládání a přístupu k datům v dokumentu můžete použít účet rozhraní SQL API v Azure Cosmos DB. V tomto kurzu se dozvíte, jak vytvořit aplikaci Java s asynchronní sadou Java SDK pro ukládání a správu dat dokumentů. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření účtu Azure Cosmos a připojení k němu
> * Konfigurace řešení
> * Vytvoření kolekce
> * Vytvoření dokumentů JSON
> * Dotazování na kolekci

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte následující prostředky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads).

* [Java Development Kit (JDK) 8+](/java/azure/jdk/?view=azure-java-stable).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Vytvořte účet Azure Cosmos pomocí následujících kroků:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="clone-the-github-repository"></a><a id="GitClone"></a>Naklonujte úložiště GitHub.

Naklonujte úložiště GitHubu, [abyste mohli začít používat Azure Cosmos DB a Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Například z místního adresáře spusťte následující příkaz, který načte ukázkový projekt místně.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

Adresář obsahuje `pom.xml` soubor a `src/main/java/com/microsoft/azure/cosmosdb/sample` složku obsahující zdrojový kód Java, včetně. `Main.java` Projekt obsahuje kód potřebný k provádění operací s Azure Cosmos DB, jako je vytváření dokumentů a dotazování na data v rámci kolekce. `pom.xml` Soubor obsahuje závislost na [Azure Cosmos DB Java SDK v Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a name="connect-to-an-azure-cosmos-account"></a><a id="Connect"></a>Připojení k účtu Azure Cosmos

Dále přejděte zpět na [Azure Portal](https://portal.azure.com) a získejte koncový bod a primární hlavní klíč. Koncový bod a primární klíč služby Azure Cosmos DB jsou potřeba k tomu, aby aplikace věděla, kam se má připojit, a aby služba Azure Cosmos DB důvěřovala připojení aplikace. Soubor `AccountSettings.java` uchovává hodnoty primárního klíče a identifikátoru URI. 

V Azure Portal přejděte na účet Azure Cosmos a klikněte na **klíče**. Zkopírujte hodnoty identifikátoru URI a PRIMÁRNÍHO KLÍČE z webu Azure Portal a vložte je do souboru `AccountSettings.java`. 

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

## <a name="initialize-the-client-object"></a>Inicializovat objekt klienta

Inicializujte objekt klienta pomocí hodnot identifikátor URI hostitele a primárního klíče definovaného v souboru AccountSettings. Java.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a name="create-a-database"></a><a id="CreateDatabase"></a>Vytvoření databáze

Vytvořte databázi Azure Cosmos pomocí `createDatabaseIfNotExists()` metody třídy DocumentClient. Databáze je logický kontejner úložiště dokumentů JSON rozděleného mezi kolekcemi.

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

## <a name="create-a-collection"></a><a id="CreateColl"></a>Vytvoření kolekce

Kolekci můžete vytvořit pomocí `createDocumentCollectionIfNotExists()` metody třídy DocumentClient. Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace.

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

## <a name="create-json-documents"></a><a id="CreateDoc"></a>Vytvoření dokumentů JSON

Vytvořte dokument pomocí metody metody CreateDocument třídy DocumentClient. Dokumenty představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete vložit jeden nebo více dokumentů. Soubor src/Main/Java/com/Microsoft/Azure/cosmosdb/Sample/rodin. Java definuje dokumenty JSON rodiny. 

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

## <a name="query-azure-cosmos-db-resources"></a><a id="Query"></a>Dotazy Azure Cosmos DB prostředky

Azure Cosmos DB podporuje bohaté dotazy na dokumenty JSON uložené v každé z kolekcí. Následující vzorový kód ukazuje, jak zadávat dotazy na dokumenty v Azure Cosmos DB pomocí syntaxe SQL s `queryDocuments` metodou.

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

## <a name="run-your-java-console-application"></a><a id="Run"></a>Spuštění konzolové aplikace v jazyce Java

Chcete-li spustit aplikaci z konzoly, otevřete složku projektu a zkompilujte pomocí Maven:

```bash
mvn package
```

Spuštěním příkazu `mvn package` se z nástroje Maven stáhne nejnovější verze knihovny Azure Cosmos DB a vytvoří se soubor `GetStarted-0.0.1-SNAPSHOT.jar`. Potom spusťte aplikaci následujícím příkazem:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Právě jste dokončili tento kurz NoSQL a máte funkční konzolovou aplikaci jazyka Java.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už je nepotřebujete, můžete odstranit skupinu prostředků, účet Azure Cosmos a všechny související prostředky. Pokud to chcete udělat, vyberte skupinu prostředků pro virtuální počítač, vyberte **Odstranit**a pak potvrďte název skupiny prostředků, která se má odstranit.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit aplikaci Java s asynchronní sadou Java SDK pro správu dat rozhraní SQL API v Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Vytvoření konzolové aplikace Node.js s využitím sady JavaScript SDK a služby Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
