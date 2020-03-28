---
title: 'Kurz: Vytvoření aplikace Java s Azure Cosmos DB Async Java SDK pro správu dat v účtu SQL API'
description: Tento kurz ukazuje, jak ukládat a přistupovat k datům v rámci účtu SQL API v Azure Cosmos DB pomocí aplikace Async Java.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 8704e399156b9cfc6b04ff47af49b956b597a539
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75444889"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Kurz: Vytvoření java aplikace s asynchronní sadou Java SDK pro správu dat uložených v účtu SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Jako vývojář můžete mít aplikace, které používají data dokumentu NoSQL. Účet ROZHRANÍ SQL API v Azure Cosmos DB můžete použít k ukládání a přístupu k těmto datům dokumentu. V tomto kurzu se můžete sestavovat java aplikaci pomocí sady Async Java SDK pro ukládání a správu dat dokumentu. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytváření a připojování k účtu Azure Cosmos
> * Konfigurace řešení
> * Vytvoření kolekce
> * Vytvoření dokumentů JSON
> * Dotazování na kolekci

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte následující prostředky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads).

* [Java Development Kit (JDK) 8+](https://aka.ms/azure-jdks).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Vytvořte účet Azure Cosmos pomocí následujících kroků:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="clone-the-github-repository"></a><a id="GitClone"></a>Klonování úložiště GitHub

Klonovat úložiště GitHub pro [začínáme s Azure Cosmos DB a Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Například z místního adresáře, spusťte následující načíst ukázkový projekt místně.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

Adresář obsahuje `pom.xml` soubor a `src/main/java/com/microsoft/azure/cosmosdb/sample` složku obsahující zdrojový kód `Main.java`Javy, včetně . Projekt obsahuje kód potřebný k provádění operací s Azure Cosmos DB, jako je vytváření dokumentů a dotazování dat v rámci kolekce. Soubor `pom.xml` obsahuje závislost na [Azure Cosmos DB Java SDK na Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a name="connect-to-an-azure-cosmos-account"></a><a id="Connect"></a>Připojení k účtu Azure Cosmos

Dále přejděte zpět na [Azure Portal](https://portal.azure.com) a získejte koncový bod a primární hlavní klíč. Koncový bod a primární klíč služby Azure Cosmos DB jsou potřeba k tomu, aby aplikace věděla, kam se má připojit, a aby služba Azure Cosmos DB důvěřovala připojení aplikace. Soubor `AccountSettings.java` uchovává hodnoty primárního klíče a identifikátoru URI. 

Na webu Azure Portal přejděte na svůj účet Azure Cosmos a klikněte na **Keys**. Zkopírujte hodnoty identifikátoru URI a PRIMÁRNÍHO KLÍČE z webu Azure Portal a vložte je do souboru `AccountSettings.java`. 

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

Inicializovat objekt klienta pomocí identifikátoru URI hostitele a hodnot primárního klíče definovaných v souboru AccountSettings.java.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a name="create-a-database"></a><a id="CreateDatabase"></a>Vytvoření databáze

Vytvořte databázi Azure Cosmos pomocí `createDatabaseIfNotExists()` metody DocumentClient třídy. Databáze je logický kontejner úložiště dokumentů JSON rozděleného mezi kolekcemi.

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

Kolekci můžete vytvořit pomocí `createDocumentCollectionIfNotExists()` metody DocumentClient třídy. Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace.

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

Vytvořte dokument pomocí metody createDocument třídy DocumentClient. Dokumenty představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete vložit jeden nebo více dokumentů. Soubor "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" definuje rodinné dokumenty JSON. 

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

## <a name="query-azure-cosmos-db-resources"></a><a id="Query"></a>Dotaz na prostředky Azure Cosmos DB

Azure Cosmos DB podporuje bohaté dotazy na dokumenty JSON uložené v každé z kolekcí. Následující ukázkový kód ukazuje, jak se dotazovat na `queryDocuments` dokumenty v Azure Cosmos DB pomocí syntaxe SQL s metodou.

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

## <a name="run-your-java-console-application"></a><a id="Run"></a>Spuštění aplikace konzoly Java

Chcete-li spustit aplikaci z konzoly, přejděte do složky projektu a zkompilujte pomocí maven:

```bash
mvn package
```

Spuštěním příkazu `mvn package` se z nástroje Maven stáhne nejnovější verze knihovny Azure Cosmos DB a vytvoří se soubor `GetStarted-0.0.1-SNAPSHOT.jar`. Potom spusťte aplikaci následujícím příkazem:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Nyní jste dokončili tento kurz NoSQL a máte funkční konzolovou aplikaci Java.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nejsou potřeba, můžete odstranit skupinu prostředků, účet Azure Cosmos a všechny související prostředky. Chcete-li tak učinit, vyberte skupinu prostředků pro virtuální počítač, vyberte **Odstranit**a potvrďte název skupiny prostředků, kterou chcete odstranit.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit java aplikaci s asynchronní sadou Java SDK pro správu dat rozhraní SQL API v Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Vytvoření konzolové aplikace Node.js s využitím sady JavaScript SDK a služby Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
