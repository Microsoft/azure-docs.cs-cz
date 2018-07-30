---
title: Sestavení aplikace v Javě pomocí sady SDK Async Java databáze Azure Cosmos DB | Microsoft Docs
description: V tomto kurzu se dozvíte, jak použít účty rozhraní SQL API databáze Azure Cosmos DB k ukládání dat a přístupu k nim pomocí aplikace v Async Java.
keywords: nosql tutorial, online database, java console application
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
ms.openlocfilehash: 2ea09b8f138f9cb6729af0e0aff1500caccde10f
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163275"
---
# <a name="build-a-java-application-by-using-azure-cosmos-db-async-java-sdk"></a>Sestavení aplikace v Javě pomocí sady SDK Async Java databáze Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js pro MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
>  
> 

Azure Cosmos DB je globálně distribuovaná databáze pro více modelů. V tomto kurzu se dozvíte, jak použít účty rozhraní SQL API databáze Azure Cosmos DB k ukládání dat a přístupu k nim pomocí aplikace v Async Java. 

Kurz zahrnuje:

* Vytvoření účtu služby Azure Cosmos DB a připojení k němu
* Konfiguraci řešení
* Vytvoření kolekce
* Vytvoření dokumentů JSON
* Dotazování na kolekci

Můžeme začít!

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že máte následující:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads)
* [Java Development Kit (JDK) 8+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB
Vytvořme účet služby Azure Cosmos DB. Pokud už máte účet, který chcete použít, můžete přeskočit k části [Klonování projektu z GitHubu](#GitClone). Pokud používáte emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md) a přeskočte k části [Klonování projektu z GitHubu](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Krok 2: Klonování projektu z GitHubu
Můžete začít naklonováním úložiště GitHub pro projekt [Začínáme se službou Azure Cosmos DB a Javou](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Například spusťte z místního adresáře následující příkaz, který načte ukázkový projekt pro místní použití.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started

```
Adresář obsahuje soubor `pom.xml` pro projekt a složku `src/main/java/com/microsoft/azure/cosmosdb/sample` obsahující zdrojový kód Java, včetně souboru `Main.java`, který ukazuje, jak s Azure Cosmos DB provádět jednoduché operace, jako je vytváření dokumentů a dotazování dat v kolekci. Soubor `pom.xml` obsahuje závislost na sadu [Azure Cosmos B Java SDK v nástroji Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Krok 3: Připojení k účtu služby Azure Cosmos DB
Dále přejděte zpět na [Azure Portal](https://portal.azure.com) a získejte koncový bod a primární hlavní klíč. Koncový bod a primární klíč služby Azure Cosmos DB jsou potřeba k tomu, aby aplikace věděla, kam se má připojit, a aby služba Azure Cosmos DB důvěřovala připojení aplikace. Soubor `AccountSettings.java` uchovává hodnoty primárního klíče a identifikátoru URI. 

Na webu Azure Portal přejděte do účtu služby Azure Cosmos DB a klikněte na **Klíče**. Zkopírujte hodnoty identifikátoru URI a PRIMÁRNÍHO KLÍČE z webu Azure Portal a vložte je do souboru `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos DB account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos DB account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Snímek obrazovky webu Azure Portal, který se v kurzu NoSQL používá k vytvoření konzolové aplikace v jazyce Java. Ukazuje účet služby Azure Cosmos DB se zvýrazněným aktivním centrem, zvýrazněným tlačítkem KLÍČE v okně účtu služby Azure Cosmos DB a zvýrazněnými hodnotami URI, PRIMÁRNÍ KLÍČ a SEKUNDÁRNÍ KLÍČ v okně Klíče.][keys]

## <a name="step-4-initialize-the-client-object"></a>Krok 4: Inicializace objektu klienta
Inicializujte objekt klienta pomocí hodnot identifikátoru URI hostitele a primárního klíče, které jsou definované v souboru „AccountSettings.java“.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Krok 5: Vytvoření databáze

[Databázi](sql-api-resources.md#databases) Azure Cosmos DB je možné vytvořit pomocí metody createDatabaseIfNotExists() třídy DocumentClient. Databáze je logický kontejner úložiště dokumentů JSON rozděleného mezi kolekcemi.

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

## <a id="CreateColl"></a>Krok 6: Vytvoření kolekce

> [!WARNING]
> **createCollection** vytvoří novou kolekci s vyhrazenou propustností, za kterou se hradí poplatky. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 
Kolekci je možné vytvořit pomocí metody createDocumentCollectionIfNotExists() třídy DocumentClient. Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace.

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

## <a id="CreateDoc"></a>Krok 7: Vytvoření dokumentů JSON

[Dokument](sql-api-resources.md#documents) je možné vytvořit pomocí metody createDocument třídy DocumentClient. Dokumenty představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete vložit jeden nebo více dokumentů. Soubor „src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java“ definuje rodinu dokumentů JSON. 

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

## <a id="Query"></a>Krok 8: Dotazování prostředků Azure Cosmos DB

Azure Cosmos DB podporuje bohaté [dotazy](sql-api-sql-query.md) na dokumenty JSON uložené v každé z kolekcí. Následující ukázkový kód ukazuje dotazování na dokumenty ve službě Azure Cosmos DB pomocí syntaxe SQL a metody [queryDocuments](/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments).

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

## <a id="Run"></a>Krok 9: Spuštění celé konzolové aplikace jazyka Java
Chcete-li aplikaci spustit z konzoly, přejděte do složky projektu a proveďte kompilaci pomocí nástroje Maven:

```bash
mvn package
```

Spuštěním příkazu `mvn package` se z nástroje Maven stáhne nejnovější verze knihovny Azure Cosmos DB a vytvoří se soubor `GetStarted-0.0.1-SNAPSHOT.jar`. Potom spusťte aplikaci následujícím příkazem:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```
Blahopřejeme! Dokončili jste tento kurz NoSQL a máte funkční konzolovou aplikaci jazyka Java!

## <a name="next-steps"></a>Další kroky
* Chcete kurz vývoje webové aplikace v jazyce Java? Viz [Vytvoření webové aplikace pomocí Javy a služby Azure Cosmos DB](sql-api-java-application.md).
* Zjistěte, jak [monitorovat účet služby Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
