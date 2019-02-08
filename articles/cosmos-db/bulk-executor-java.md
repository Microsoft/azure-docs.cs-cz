---
title: Použití knihovny Java prováděcí modul hromadného hromadně importovat a aktualizovat operace ve službě Azure Cosmos DB
description: Hromadný import a aktualizace pomocí hromadné prováděcí modul Java knihovna dokumenty Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 8254e3221fee3d76e2d27715f76c26397c309f08
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862713"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Použití knihovny Java prováděcí modul hromadného budou provádět hromadné operace s daty služby Azure Cosmos DB

Tento kurz obsahuje pokyny k používání knihovny Java prováděcí modul hromadného rozhraní Azure Cosmos DB importovat a aktualizovat dokumenty Azure Cosmos DB. Další informace o hromadně prováděcí modul knihovny a jak vám může pomoct využít mimořádně velkou propustnost a úložiště, najdete v článku [hromadně přehled knihovny prováděcí modul](bulk-executor-overview.md) článku. V tomto kurzu sestavení aplikace v Javě, která generuje náhodné dokumentů a budou hromadně importovat do kontejneru Azure Cosmos DB. Po naimportování pak hromadně aktualizovat některé vlastnosti dokumentu. 

Hromadné prováděcí modul knihovny je v současné době nepodporuje rozhraní SQL API služby Azure Cosmos DB a pouze účty rozhraní Gremlin API. Tento článek popisuje, jak pomocí hromadné knihovny .net prováděcí modul s účty SQL API. Další informace o použití knihovny hromadné prováděcí modul .NET pomocí rozhraní Gremlin API, najdete v článku [provádět hromadné operace v rozhraní Gremlin API služby Azure Cosmos DB](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.  

* [Službu Azure Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků. Nebo můžete použít [emulátor služby Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) s `https://localhost:8081` koncového bodu. Primární klíč je uvedený v části [Ověřování požadavků](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)  
  - Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.  

  - Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.

* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv [Maven](https://maven.apache.org/).  
  
  - Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.

* Vytvoření účtu rozhraní SQL API služby Azure Cosmos DB pomocí kroků popsaných v [vytvoření databázového účtu](create-sql-api-java.md#create-a-database-account) část tohoto článku rychlý start Java.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem stažením ukázkové aplikace v Javě z Githubu. Tato aplikace provádí hromadné operace s daty služby Azure Cosmos DB. Klonování aplikace, otevřete příkazový řádek, přejděte do adresáře, kam chcete zkopírovat aplikace a spusťte následující příkaz:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Naklonované úložiště obsahuje dvě ukázky "hromadný import" a "bulkupdate", "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor" složkou. Aplikace "hromadný import" generuje náhodné dokumenty a naimportuje je do služby Azure Cosmos DB. Aplikace "bulkupdate" aktualizuje některé dokumenty ve službě Azure Cosmos DB. V následujících částech Podíváme se kód v každém z těchto ukázkových aplikací. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Hromadné import dat do služby Azure Cosmos DB

1. Azure Cosmos DB připojovací řetězce jsou číst jako argumenty a přiřazen do proměnné definované v souboru CmdLineConfiguration.java.  

2. Vedle objektu DocumentClient je inicializována pomocí následujících příkazů:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. DocumentBulkExecutor objekt je inicializován s hodnotami vysoké opakování pro doba čekání a omezuje požadavky. A pak jsou nastaveny na hodnotu 0 pro předání řízení zahlcení DocumentBulkExecutor po dobu jeho existence.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
```

4. Volání hodnotu importAll rozhraní API, které generuje náhodné dokumenty pro hromadný import do kontejneru Azure Cosmos DB. Konfigurace příkazový řádek v souboru CmdLineConfiguration.java můžete nakonfigurovat.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
```
   Rozhraní API hromadného importu přijímá kolekce dokumentů, serializací JSON a má následující syntaxi, další podrobnosti naleznete [dokumentace k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Metoda hodnotu importAll přijímá následující parametry:
 
   |**Parametr**  |**Popis**  |
   |---------|---------|
   |isUpsert    |   Příznak, který chcete povolit upsert dokumenty. Pokud dokument s daným ID již existuje, se aktualizuje.  |
   |disableAutomaticIdGeneration     |   Příznak, který chcete zakázat automatické generování ID. Ve výchozím nastavení, je nastavena na hodnotu true.   |
   |maxConcurrencyPerPartitionRange    |  Maximální stupeň souběžnosti na rozsah klíče oddílu. Výchozí hodnota je 20.  |

   **Hromadný import definice objektu odpovědi** výsledek hromadný import volání rozhraní API obsahuje následující dvě metody get:

   |**Parametr**  |**Popis**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Celkový počet dokumentů, které byly úspěšně naimportovány z dokumentů zadaný pro hromadného importu volání rozhraní API.      |
   |dvojité getTotalRequestUnitsConsumed()   |  Jednotky celkový počet žádostí (RU) využívaný hromadného importu volání rozhraní API.       |
   |Doba trvání getTotalTimeTaken()   |    Celková doba, za kterou hromadný import volání rozhraní API k dokončení provádění.     |
   |Seznam<Exception> getErrors() |  Získá seznam chyb, pokud některé dokumenty ze služby batch, zadaný do hromadného importu se nepodařilo získat vložit volání rozhraní API.       |
   |Seznam<Object> getBadInputDocuments()  |    Seznam chybný formát dokumenty, které nebyly úspěšně naimportovány hromadně importovat volání rozhraní API. Uživatel musí opravit vrácených dokumentů a zkuste import zopakovat. Dokumenty ve formátu chybný obsahovat dokumenty, jejichž ID hodnota není řetězec (datový typ null nebo jakékoli jiné se považuje za neplatný).     |

5. Až budete mít hromadného importu aplikace připravené, sestavení pomocí příkazu 'mvn čisté balíčku' nástroj příkazového řádku ze zdroje. Tento příkaz vygeneruje v cílové složce soubor jar:  

   ```java
   mvn clean package
   ```

6. Po vygenerování závislosti cílů, můžete vyvolat programu pro import aplikace hromadně pomocí následujícího příkazu:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Programu pro import hromadné vytvoří novou databázi a kolekci s názvem databáze, název kolekce a propustnost hodnoty zadané v souboru App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Hromadné aktualizace dat ve službě Azure Cosmos DB

Pomocí rozhraní API BulkUpdateAsync můžete aktualizovat existující dokumenty. V tomto příkladu bude nastavena na novou hodnotu pole název a popis pole odebrat z existujících dokumentů. Operace aktualizace úplnou sadu podporovaných polí naleznete v tématu [dokumentace k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definuje aktualizace položek spolu s odpovídající operace aktualizace pole. V tomto příkladu použijete SetUpdateOperation aktualizovat pole s názvem a UnsetUpdateOperation odebrání pole Popis všechny dokumenty. Můžete také provádět jiné operace jako přírůstek pole dokumentu podle konkrétní hodnoty, nasdílení změn určité hodnoty do pole nebo odebrat konkrétní hodnoty z pole. Další informace o různých způsobech hromadné aktualizace rozhraní API poskytuje, najdete v článku [dokumentace k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Volání rozhraní API, které generuje náhodné dokumenty hromadně importovat do kontejneru Azure Cosmos DB bude potom updateAll. Můžete nakonfigurovat příkazového řádku konfigurace mají být předány v CmdLineConfiguration.java souboru.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Hromadná aktualizace rozhraní API přijímá kolekci položek, které chcete aktualizovat. Každá položka aktualizace určuje seznam operací aktualizace pole mají být provedeny pro dokument určený ID a hodnotu klíče oddílu. Další podrobnosti najdete v tématu [dokumentace k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   UpdateAll – metoda přijímá následující parametry:

   |**Parametr** |**Popis** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Maximální stupeň souběžnosti na rozsah klíče oddílu. Výchozí hodnota je 20.  |
 
   **Hromadný import definice objektu odpovědi** výsledek hromadný import volání rozhraní API obsahuje následující dvě metody get:

   |**Parametr** |**Popis**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Celkový počet dokumentů, které byly úspěšně aktualizovány mimo dokumenty zadaný pro hromadné aktualizace volání rozhraní API.      |
   |dvojité getTotalRequestUnitsConsumed() |  Jednotky celkový počet žádostí (RU) využívaný hromadné aktualizace volání rozhraní API.       |
   |Doba trvání getTotalTimeTaken()  |   Celková doba, za kterou hromadného aktualizujte volání rozhraní API k dokončení provádění.      |
   |Seznam<Exception> getErrors()   |     Získá seznam chyb, pokud hromadné aktualizace volání rozhraní API se nepodařilo získat vloží některé dokumenty ze služby batch.      |

3. Až budete mít hromadné aktualizace aplikace připravené, sestavení pomocí příkazu 'mvn čisté balíčku' nástroj příkazového řádku ze zdroje. Tento příkaz vygeneruje v cílové složce soubor jar:  

   ```
   mvn clean package
   ```

4. Po vygenerování závislosti cílů, můžete vyvolat hromadné aktualizace aplikace pomocí následujícího příkazu:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Tipy pro zvýšení výkonu 

Zvažte následující body pro zajištění lepšího výkonu při použití hromadné prováděcí modul knihovny:

* Pro zajištění nejlepšího výkonu spusťte aplikaci z virtuálního počítače Azure ve stejné oblasti jako oblast zápisu účtu Cosmos DB.  
* Pro dosažení vyšší propustnost:  

   * Velikost haldy JVM nastavena na hodnotu dostatečně velký, aby jakýkoli problém paměti při zpracování velkého množství dokumentů. Navržená velikost haldy: maximální počet (3GB, 3 * sizeof (všechny dokumenty předaný k hromadnému importu rozhraní API v jedné dávce)).  
   * Existuje předzpracování času, které získáte vyšší propustnost při provádění operace hromadného s velkým množstvím dokumentů. Proto pokud chcete importovat 10 000 000 dokumentů, zprovoznění hromadný import 10krát na 10 hromadné dokumentů každý o velikosti 1 000 000 je vhodnější srovnání se spouštěním hromadný import 100krát na 100 hromadné dokumentů všech velikostí 100 000 dokumentů.  

* Doporučuje se vytvořit instanci jednoho objektu DocumentBulkExecutor pro celou aplikaci v rámci jeden virtuální počítač, který odpovídá konkrétní kontejner Azure Cosmos DB.  

* Protože spuštění operace rozhraní API jednou hromadnou spotřebovává velké blok klientského počítače procesoru a sítě vstupně-výstupních operací. To se stane, vytvořením více úkolů interně, vyhněte se vytváření podřízeného procesu více souběžných úloh v rámci procesu aplikace, které volá každé rozhraní API provádění hromadné operace. Pokud volání jednou hromadnou operaci rozhraní API běžící na jeden virtuální počítač nemůže využívat propustnosti celého kontejneru (Pokud se váš kontejner propustnost > 1 milion RU/s), je vhodnější k vytvoření samostatných virtuálních počítačů současně provést hromadné volání operace rozhraní API.

    
## <a name="next-steps"></a>Další postup
* Další informace o podrobnosti balíčku maven a knihovny Java prováděcí modul hromadného poznámky k verzi najdete v tématu[hromadně podrobnosti o sadě SDK prováděcí modul](sql-api-sdk-bulk-executor-java.md).


