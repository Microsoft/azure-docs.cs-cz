---
title: Použití knihovny BulkExecutor Java provádět hromadné operace v Azure Cosmos DB | Microsoft Docs
description: Pomocí knihovny Azure Cosmos DB BulkExecutor Java hromadný import a aktualizace dokumenty do kolekcí Azure Cosmos DB.
keywords: Hromadné vykonavatele Java
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 6e9340e5db78315470052e0c0c70eeef6bf6dafa
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="use-bulkexecutor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Použití knihovny BulkExecutor Java hromadné operace v Azure Cosmos DB dat

V tomto kurzu poskytuje pokyny k používání Azure Cosmos DB hromadné vykonavatele Java knihovny importu a aktualizaci dokumentů Azure Cosmos DB. Další informace o BulkExecutor knihovny a jak ho pomáhá využívat obrovskou propustnost a úložiště najdete v tématu [přehled knihovny BulkExecutor](bulk-executor-overview.md) článku. V tomto kurzu vytvoříte aplikaci Java, která generuje náhodné dokumenty a jsou hromadně importovat kolekci Azure Cosmos DB. Po importu, hromadně aktualizovat některé vlastnosti dokumentu. 

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.  

* [Službu Azure Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků. Nebo můžete použít [emulátoru DB Cosmos Azure](https://docs.microsoft.com/azure/cosmos-db/local-emulator) s `https://localhost:8081` identifikátor URI. Primární klíč je k dispozici v [ověřování požadavků](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  
  - Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.  

  - Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.

* [Stáhněte](http://maven.apache.org/download.cgi) a [nainstalujte](http://maven.apache.org/install.html) binární archiv [Maven](http://maven.apache.org/).  
  
  - Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.

* Vytvoření účtu Azure Cosmos DB SQL API pomocí kroků popsaných v [vytvoření databázového účtu](create-sql-api-java.md#create-a-database-account) v jazyce Java rychlý start článku.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď umožňuje přepnout do práce s kódem stáhněte ukázkovou aplikaci Java z Githubu. Tato aplikace provede hromadné operace s daty Azure Cosmos DB. Klonování aplikace, otevřete příkazový řádek, přejděte do adresáře, kam chcete zkopírovat aplikace a spusťte následující příkaz:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Klonovaný úložiště obsahuje dvě ukázky "hromadný import" a "bulkupdate" relativní vzhledem ke složce "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". Aplikace "hromadný import" generuje náhodné dokumenty a importuje je do Azure Cosmos DB. Aplikace "bulkupdate" aktualizuje některé dokumenty v Azure Cosmos DB. V následujících částech budeme zkontrolujete kód v každé z těchto ukázkových aplikací. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Hromadný import dat do databáze Azure Cosmos

1. Připojovací řetězce Azure Cosmos DB přečíst jako argumenty a přiřazený k proměnné definované v souboru CmdLineConfiguration.java.  

2. Dále je objekt DocumentClient inicializovat pomocí následující příkazy:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. Objekt DocumentBulkExecutor je inicializován s hodnotami vysoké opakování dobu čekání a omezení požadavků. A potom jsou nastaveny na hodnotu 0 předat řízení zahlcení DocumentBulkExecutor pro celé jeho životnosti.  

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
     offerThroughput) // throughput you want to allocate for bulk import out of the collection's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
```

4. Volání hodnotu importAll rozhraní API, které generuje náhodné dokumentů hromadný import do kolekci Azure Cosmos DB. Můžete nakonfigurovat konfigurace příkazového řádku v souboru CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
```
   Rozhraní API pro hromadný import přijímá kolekci serializací JSON dokumentů a má následující syntaxi, další podrobnosti naleznete [dokumentaci k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

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
   |isUpsert    |   Příznaku pro povolení upsert dokumentů. Pokud dokument s uvedenými ID již existuje, jsou aktualizovány.  |
   |disableAutomaticIdGeneration     |   Příznak zakázat automatické generování ID. Ve výchozím nastavení je nastaven na hodnotu true.   |
   |maxConcurrencyPerPartitionRange    |  Maximální stupeň souběžnosti na rozsah klíče oddílu. Výchozí hodnota je 20.  |

   **Hromadný import definice objektu odpovědi** výsledek hromadným importem volání rozhraní API obsahuje následující metody get:

   |**Parametr**  |**Popis**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Celkový počet dokumentů, které byly úspěšně importovány mimo dokumenty zadané hromadného importu volání rozhraní API.      |
   |dvojité getTotalRequestUnitsConsumed()   |  Jednotek celkový žádosti (RU), které spotřebovávají hromadného importu volání rozhraní API.       |
   |Doba trvání getTotalTimeTaken()   |    Celková doba, za kterou hromadným importem volání rozhraní API pro dokončení provádění.     |
   |Seznam<Exception> getErrors() |  Získá seznam chyb, pokud některé dokumenty mimo batch zadané hromadného importu se nepodařilo získat vložit volání rozhraní API.       |
   |Seznam<Object> getBadInputDocuments()  |    Seznam chybný formát dokumenty, které nebyly úspěšně importovány v hromadného importu volání rozhraní API. Uživatel by měl opravte dokumenty vrácené a opakujte import. Dokumenty ve formátu chybný obsahovat dokumenty, jehož ID hodnota není řetězec (hodnotu null nebo jakýchkoli jiných datový typ je považovány za neplatné).     |

5. Až budete mít hromadného importu aplikace připravené, vytvořte pomocí příkazu "mvn čisté balíčku, nástroj příkazového řádku ze zdroje. Tento příkaz vytvoří soubor jar v cílové složce:  

   ```java
   mvn clean package
   ```

6. Po vygenerování cílové závislosti můžete vyvolat hromadné – Importér aplikace pomocí následujícího příkazu:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint URI>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Program pro import hromadné vytvoří novou databázi a kolekci s názvem databáze, název kolekce a propustnost hodnoty zadané v souboru App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Hromadné aktualizace dat v Azure Cosmos DB

Pomocí rozhraní API BulkUpdateAsync můžete aktualizovat existující dokumenty. V tomto příkladu bude nastavena na novou hodnotu pole název a popis pole odebrat z existujících dokumentů. Pro úplnou sadu podporované pole operace aktualizace, najdete v části [dokumentaci k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definuje aktualizace položek spolu s odpovídající pole operace aktualizace. V tomto příkladu použijete SetUpdateOperation aktualizovat pole název a UnsetUpdateOperation pole popisu odebrání všechny dokumenty. Můžete také provést další operace, jako je přírůstek pole dokumentu určitou hodnotu, push konkrétní hodnoty do pole aplikace pole nebo odebrat konkrétní hodnotu z pole pole. Další informace o různých metod, které jsou součástí hromadné aktualizace rozhraní API najdete v tématu [dokumentaci k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

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

2. Volání UpdateAll – rozhraní API, které generuje náhodné dokumentům tak, aby se pak hromadně importovat kolekci Azure Cosmos DB. Můžete nakonfigurovat konfiguraci příkazového řádku mají být předány v souboru CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Hromadné aktualizace rozhraní API přijme kolekce položek, které mají být aktualizovány. Každá položka aktualizace určuje seznam operací aktualizace pole mají být provedeny se identifikovanou pomocí ID a hodnotu klíče oddílu dokumentu. Další podrobnosti najdete v tématu [dokumentaci k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   UpdateAll – metoda přijímá následující parametry:

   |**Parametr** |**Popis** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Maximální stupeň souběžnosti na rozsah klíče oddílu. Výchozí hodnota je 20.  |
 
   **Hromadný import definice objektu odpovědi** výsledek hromadným importem volání rozhraní API obsahuje následující metody get:

   |**Parametr** |**Popis**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Celkový počet dokumentů, které byly úspěšně aktualizovány mimo dokumenty zadaný do hromadné aktualizace volání rozhraní API.      |
   |dvojité getTotalRequestUnitsConsumed() |  Jednotek celkový žádosti (RU) spotřebovávána hromadné aktualizace volání rozhraní API.       |
   |Doba trvání getTotalTimeTaken()  |   Celková doba, kterou hromadným aktualizujte volání rozhraní API pro dokončení provádění.      |
   |Seznam<Exception> getErrors()   |     Získá seznam chyb, pokud některé dokumenty mimo dávky zadaná pro volání rozhraní API hromadné aktualizace se nepodařilo získat vložit.      |

3. Až budete mít hromadné aktualizace aplikace připravené, vytvořte pomocí příkazu "mvn čisté balíčku, nástroj příkazového řádku ze zdroje. Tento příkaz vytvoří soubor jar v cílové složce:  

   ```
   mvn clean package
   ```

4. Po vygenerování cílové závislosti můžete vyvolat aplikace hromadné aktualizace pomocí následujícího příkazu:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint URI>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Tipy pro zvýšení výkonu 

Při použití knihovny BulkExecutor, zvažte následující body pro dosažení vyššího výkonu:

* Pro nejlepší výkon spusťte aplikaci z virtuálního počítače Azure ve stejné oblasti jako zápisu oblast účtu Cosmos DB.  
* K dosažení vyšší propustnost:  

   * Nastavte velikost haldy JVM na dostatečně velký počet předejdete jakýkoli problém paměti při zpracování velkého množství dokumentů. Navrhované velikost haldy: maximální počet (3GB, 3 * sizeof (všechny dokumenty předaný hromadný import rozhraní API v jedné dávce)).  
   * Je předběžného zpracování doba, kvůli které budete mít vyšší propustnost při provádění operací hromadné s velkým počtem dokumentů. Pokud chcete importovat 10 000 000 dokumentů, systémem hromadným importem 10krát 10 hromadné dokumentů každý o velikosti 1 000 000 Ano, je vhodnější než spuštění hromadným importem 100krát na 100 hromadné dokumentů všech velikost 100 000 dokumentů.  

* Doporučuje se vytvořit instanci jednoho objektu DocumentBulkExecutor pro celou aplikaci v rámci jedné virtuální počítač, který odpovídá konkrétní kolekci Azure Cosmos DB.  

* Vzhledem k tomu, že spuštění jedné hromadné operace rozhraní API spotřebuje velký blok klientský počítač procesoru a sítě vstupně-výstupní operace. To se stane vytvořením několika úloh interně, vyhněte se například více souběžných úloh v rámci procesu vaší aplikace, které volá každé rozhraní API provádění hromadné operace. Pokud volání jedné hromadné operace rozhraní API systémem jeden virtuální počítač nemůže využívat propustnost celou kolekci (Pokud vaše kolekce propustnost > 1 milion RU/s), je vhodnější k vytvoření samostatných virtuálních počítačů současně provést hromadné volání operace rozhraní API.

    
## <a name="next-steps"></a>Další postup
* Další informace o podrobnosti balíčku maven a poznámky k BulkExecutor Java knihovny verzi najdete v tématu[BulkExecutor SDK podrobnosti](sql-api-sdk-bulk-executor-java.md).


