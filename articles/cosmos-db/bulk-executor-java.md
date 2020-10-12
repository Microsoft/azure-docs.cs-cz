---
title: Použití hromadného vykonavatele Java Library v Azure Cosmos DB k provádění hromadných operací importu a aktualizace
description: Hromadný import a aktualizace Azure Cosmos DBch dokumentů pomocí hromadné prováděcí knihovny Java
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 08/26/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: a9501df45d598c85f8c694c5d07db4f959615c00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968177"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Použití knihovny Bulk Executor Java k provádění hromadných operací s daty služby Azure Cosmos DB

Tento kurz poskytuje pokyny k importu a aktualizaci dokumentů Azure Cosmos DB pomocí hromadného prováděcího modulu jazyka Java v Azure Cosmos DB. Další informace o hromadné prováděcí knihovně a o tom, jak vám pomůže využít obrovské propustnost a úložiště, najdete v článku [Přehled knihovny hromadného prováděcího modulu](bulk-executor-overview.md) . V tomto kurzu vytvoříte aplikaci Java, která generuje náhodné dokumenty a hromadně importuje do kontejneru Azure Cosmos. Po importu budete hromadně aktualizovat některé vlastnosti dokumentu. 

V současné době je knihovna hromadných prováděcích modulů podporovaná jenom pomocí Azure Cosmos DB SQL API a účtů rozhraní API Gremlin. Tento článek popisuje, jak používat hromadnou prováděcí knihovnu Java s účty rozhraní SQL API. Další informace o použití knihovny hromadného prováděcího modulu .NET s rozhraním Gremlin API najdete v tématu [provádění hromadných operací v rozhraní Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md). Popsaná knihovna hromadného prováděcího modulu je k dispozici pouze pro [sadu Azure Cosmos DB Java Sync SDK v2](sql-api-sdk-java.md) a jedná se o aktuální doporučené řešení pro hromadnou podporu Java. V současné době není k dispozici pro 3. x, 4. x a novější verze sady SDK.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.  

* Můžete [vyzkoušet Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, zdarma a závazků. Nebo můžete použít [emulátor Azure Cosmos DB](/azure/cosmos-db/local-emulator) u `https://localhost:8081` koncového bodu. Primární klíč je uvedený v části [Ověřování požadavků](local-emulator.md#authenticate-requests).  

* [Java Development Kit (JDK) 1.7 +](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)  
  - Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.  

  - Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.

* [Stažení](https://maven.apache.org/download.cgi) a [instalace](https://maven.apache.org/install.html) binárního archivu [Maven](https://maven.apache.org/)  
  
  - Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.

* Vytvořte Azure Cosmos DB účet rozhraní SQL API pomocí postupu popsaného v části [vytvoření databázového účtu](create-sql-api-java.md#create-a-database-account) v článku rychlý Start pro Java.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď se podíváme na práci s kódem stažením ukázkové aplikace Java z GitHubu. Tato aplikace provádí hromadné operace s Azure Cosmos DBmi daty. K naklonování aplikace otevřete příkazový řádek, přejděte do adresáře, kam chcete aplikaci zkopírovat, a spusťte následující příkaz:

```bash
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Klonovaný úložiště obsahuje dvě ukázky "bulkimport" a "bulkupdate" relativní ke složce "\azure-cosmosdb-bulkexecutor-Java-Getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". Aplikace "bulkimport" generuje náhodné dokumenty a importuje je do Azure Cosmos DB. Aplikace "bulkupdate" aktualizuje některé dokumenty v Azure Cosmos DB. V dalších částech si projdeme kód v každé z těchto ukázkových aplikací. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Hromadné importy dat do Azure Cosmos DB

1. Připojovací řetězce Azure Cosmos DB jsou čteny jako argumenty a přiřazeny k proměnným definovaným v souboru. Java CmdLineConfiguration.  

2. Další objekt DocumentClient je inicializován pomocí následujících příkazů:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. Objekt DocumentBulkExecutor je inicializován s vysokými hodnotami opakování pro čekací dobu a omezené požadavky. A pak jsou nastavené na 0, aby bylo možné předávat řízení přetížení DocumentBulkExecutor po dobu života.  

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

4. Zavolejte rozhraní API pro vyvolání, které generuje náhodné dokumenty pro hromadnou import do kontejneru Azure Cosmos. Konfigurace příkazového řádku můžete nakonfigurovat v souboru CmdLineConfiguration. Java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   Rozhraní API pro hromadné importy přijímá kolekci dokumentů serializovaných ve formátu JSON a má následující syntaxi. Další informace najdete v [dokumentaci k rozhraní API](/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Metoda pro neportál přijímá následující parametry:
 
   |**Parametr**  |**Popis**  |
   |---------|---------|
   |isUpsert    |   Příznak, který povolí Upsert dokumentů. Pokud dokument s daným ID již existuje, je aktualizován.  |
   |disableAutomaticIdGeneration     |   Příznak, který zakáže automatickou generaci IDENTIFIKÁTORů. Ve výchozím nastavení je nastavena na hodnotu true.   |
   |maxConcurrencyPerPartitionRange    |  Maximální stupeň souběžnosti na rozsah klíče oddílu. Výchozí hodnota je 20.  |

   **Definice objektu odpovědi pro hromadné importy** Výsledek volání rozhraní API hromadného importu obsahuje následující metody Get:

   |**Parametr**  |**Popis**  |
   |---------|---------|
   |int getNumberOfDocumentsImported ()  |   Celkový počet dokumentů, které byly úspěšně naimportovány z dokumentů dodaných do volání hromadného importu rozhraní API.      |
   |Dvojitá getTotalRequestUnitsConsumed ()   |  Celkový počet jednotek žádosti (RU) spotřebovaných voláním rozhraní API hromadného importu.       |
   |Doba trvání getTotalTimeTaken ()   |    Celková doba, kterou zabere volání rozhraní API hromadného importu k dokončení provádění.     |
   |Výpis \<Exception> GetErrors () |  Načte seznam chyb, pokud se nezdařila vložení některých dokumentů z dávky dodané do volání rozhraní API hromadného importu.       |
   |Seznam \<Object> getBadInputDocuments ()  |    Seznam dokumentů se špatným formátem, které nebyly úspěšně importovány v volání rozhraní API hromadného importu. Uživatel by měl opravit vrácené dokumenty a pokusit se o import znovu. Chybné – formátované dokumenty obsahují dokumenty, jejichž hodnota ID není řetězec (null nebo jakýkoli jiný datový typ se považuje za neplatné).     |

5. Po přípravě aplikace hromadného importu Sestavte nástroj příkazového řádku ze zdroje pomocí příkazu ' MVN Cleanup Package '. Tento příkaz vygeneruje soubor JAR v cílové složce:  

   ```bash
   mvn clean package
   ```

6. Po vygenerování cílů závislosti můžete vyvolat aplikaci hromadný import pomocí následujícího příkazu:  

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB's endpoint>*  -masterKey *<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Hromadný import vytvoří novou databázi a kolekci s názvem databáze, názvem kolekce a hodnotami propustnosti zadanými v souboru App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Hromadná aktualizace dat v Azure Cosmos DB

Existující dokumenty můžete aktualizovat pomocí rozhraní BulkUpdateAsync API. V tomto příkladu nastavíte pole název na novou hodnotu a odeberete pole Popis z existujících dokumentů. Úplnou sadu podporovaných operací aktualizace polí najdete v [dokumentaci k rozhraní API](/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definuje položky aktualizace spolu s odpovídajícími operacemi aktualizace polí. V tomto příkladu použijete SetUpdateOperation k aktualizaci pole název a UnsetUpdateOperation k odebrání pole Description ze všech dokumentů. Můžete také provádět jiné operace, jako je například zvýšení pole dokumentu konkrétní hodnotou, zadání specifických hodnot do pole pole nebo odebrání konkrétní hodnoty z pole Array. Další informace o různých metodách, které poskytuje rozhraní API pro hromadnou aktualizaci, najdete v [dokumentaci k rozhraní API](/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

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

2. Zavolejte rozhraní updateAll API, které generuje náhodné dokumenty, které se pak hromadně importují do kontejneru Azure Cosmos. Můžete nakonfigurovat konfigurace příkazového řádku, které se mají předávat do souboru CmdLineConfiguration. Java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Rozhraní API pro hromadnou aktualizaci přijímá kolekci položek, které se mají aktualizovat. Každá položka aktualizace určuje seznam operací aktualizace pole, které mají být provedeny u dokumentu identifikovaného ID a hodnotou klíče oddílu. Další podrobnosti najdete v [dokumentaci k rozhraní API](/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   Metoda updateAll přijímá následující parametry:

   |**Parametr** |**Popis** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Maximální stupeň souběžnosti na rozsah klíče oddílu. Výchozí hodnota je 20.  |
 
   **Definice objektu odpovědi pro hromadné importy** Výsledek volání rozhraní API hromadného importu obsahuje následující metody Get:

   |**Parametr** |**Popis**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated ()  |   Celkový počet dokumentů, které byly úspěšně aktualizovány z dokumentů dodaných do volání hromadné aktualizace rozhraní API.      |
   |Dvojitá getTotalRequestUnitsConsumed () |  Celkový počet jednotek žádosti (RU) spotřebovaných voláním rozhraní API hromadné aktualizace.       |
   |Doba trvání getTotalTimeTaken ()  |   Celková doba, kterou zabere volání rozhraní API hromadné aktualizace k dokončení provádění.      |
   |Výpis \<Exception> GetErrors ()   |       Načte seznam provozních nebo síťových problémů souvisejících s operací aktualizace.      |
   |Seznam \<BulkUpdateFailure> getFailedUpdates ()   |       Získá seznam aktualizací, které nemohly být dokončeny spolu s konkrétními výjimkami vedoucími k selhání.|

3. Po dokončení aplikace hromadné aktualizace Sestavte nástroj příkazového řádku ze zdroje pomocí příkazu ' MVN Cleanup Package '. Tento příkaz vygeneruje soubor JAR v cílové složce:  

   ```bash
   mvn clean package
   ```

4. Po vygenerování cílových závislostí můžete vyvolat aplikaci hromadné aktualizace pomocí následujícího příkazu:

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB's endpoint>* -masterKey **<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Tipy pro zvýšení výkonu 

Při použití knihovny hromadných prováděcích knihoven Vezměte v úvahu následující body:

* Nejlepšího výkonu dosáhnete, když aplikaci spustíte z virtuálního počítače Azure ve stejné oblasti jako v oblasti zápisu Cosmos DB účtu.  
* Pro dosažení vyšší propustnosti:  

   * Nastavte velikost haldy JVM na velké množství, aby nedocházelo k problémům s pamětí při zpracování velkého počtu dokumentů. Doporučená velikost haldy: max (povolenou, 3 * sizeof (všechny dokumenty předané do hromadného importu rozhraní API v jedné dávce)).  
   * K dispozici je doba předběžného zpracování, kvůli které získáte vyšší propustnost při provádění hromadných operací s velkým počtem dokumentů. Pokud tedy chcete importovat 10 000 000 dokumentů, spouští se hromadný import 10 krát 10 hromadných dokumentů. Každá velikost 1 000 000 je vhodnější než spouštění hromadného 100 importu, které se týkají 100 100 000 hromadných dokumentů.  

* Doporučuje se vytvořit instanci jednoho objektu DocumentBulkExecutor pro celou aplikaci v jednom virtuálním počítači, který odpovídá konkrétnímu kontejneru Azure Cosmos.  

* Vzhledem k tomu, že jedno spuštění hromadné operace rozhraní API spotřebovává velké množství dat procesoru klientského počítače a v/v sítě. K tomu dochází vytvořením několika úloh interně. Vyhněte se vytváření více souběžných úloh v rámci procesu aplikace při každém spuštění volání rozhraní API hromadného provozu. Pokud jedno volání rozhraní API hromadné operace běžící na jednom virtuálním počítači nedokáže spotřebovat propustnost celého kontejneru (Pokud propustnost kontejneru > 1 000 000 RU/s), je vhodnější vytvořit samostatné virtuální počítače pro souběžné volání rozhraní API hromadného provozu.

    
## <a name="next-steps"></a>Další kroky
* Další informace o podrobnostech balíčku Maven a poznámkách k verzi hromadného vykonavatele Java Library naleznete v tématu[hromadné prováděcí informace sady SDK](sql-api-sdk-bulk-executor-java.md).


