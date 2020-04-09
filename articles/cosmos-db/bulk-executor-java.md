---
title: Použití knihovny Java hromadného vykonavatele v Azure Cosmos DB k provádění operací hromadného importu a aktualizace
description: Hromadný import a aktualizace dokumentů Azure Cosmos DB pomocí knihovny Java hromadného vykonavatele
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: f5c6562c6def1fa588724b3bc5da502536b16aa9
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985639"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Použití knihovny Bulk Executor Java k provádění hromadných operací s daty služby Azure Cosmos DB

Tento kurz obsahuje pokyny k použití knihovny Java hromadného vykonavatele služby Azure Cosmos DB k importu a aktualizaci dokumentů Azure Cosmos DB. Další informace o knihovně hromadného vykonavatele a o tom, jak vám pomáhá využít masivní propustnost a úložiště, najdete v článku [přehled knihovny hromadného vykonavatele.](bulk-executor-overview.md) V tomto kurzu vytvoříte java aplikaci, která generuje náhodné dokumenty a jsou hromadně importovány do kontejneru Azure Cosmos. Po importu budete hromadně aktualizovat některé vlastnosti dokumentu. 

V současné době je knihovna hromadného prováděcího modulu podporovaná jenom účty Azure Cosmos DB SQL API a Gremlin API. Tento článek popisuje, jak používat hromadnou knihovnu Java vykonavatele s účty rozhraní SQL API. Informace o použití hromadné hostovětní ho knihovny .NET s rozhraním Gremlin API najdete [v tématu provádění hromadných operací v rozhraní API Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) než začnete.  

* Azure [Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, a to zdarma a závazků. Nebo můžete použít [Emulátor Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/local-emulator) DB `https://localhost:8081` s koncovým bodem. Primární klíč je uvedený v části [Ověřování požadavků](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1.7+](/java/azure/jdk/?view=azure-java-stable)  
  - Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.  

  - Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.

* [Stažení](https://maven.apache.org/download.cgi) a [instalace](https://maven.apache.org/install.html) binárního archivu [Maven](https://maven.apache.org/)  
  
  - Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.

* Vytvořte účet Azure Cosmos DB SQL API pomocí kroků popsaných v části [vytvořit databázový účet](create-sql-api-java.md#create-a-database-account) v článku rychlého startu java.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Nyní přepneme na práci s kódem stažením ukázkové java aplikace z GitHubu. Tato aplikace provádí hromadné operace na datech Azure Cosmos DB. Chcete-li aplikaci klonovat, otevřete příkazový řádek, přejděte do adresáře, do kterého chcete aplikaci zkopírovat, a spusťte následující příkaz:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Klonované úložiště obsahuje dva ukázky "bulkimport" a "bulkupdate" vzhledem ke složce "\azure-cosmosdb-bulkexecutor-java-get-started\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". Aplikace "bulkimport" generuje náhodné dokumenty a importuje je do Azure Cosmos DB. Aplikace "bulkupdate" aktualizuje některé dokumenty v Azure Cosmos DB. V dalších částech zkontrolujeme kód v každé z těchto ukázkových aplikací. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Hromadná data importu do Azure Cosmos DB

1. Připojovací řetězce Azure Cosmos DB se čtou jako argumenty a přiřazují se k proměnným definovaným v souboru CmdLineConfiguration.java.  

2. Dále DocumentClient objekt je inicializován pomocí následujících příkazů:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. DocumentBulkExecutor objekt je inicializován s vysokými hodnotami opakování pro čekací doby a omezené požadavky. A pak jsou nastaveny na 0 předat řízení přetížení DocumentBulkExecutor po dobu jeho životnosti.  

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

4. Volání importAll rozhraní API, které generuje náhodné dokumenty pro hromadný import do kontejneru Azure Cosmos. Konfigurace příkazového řádku můžete konfigurovat v souboru CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   Rozhraní API pro hromadný import přijímá kolekci serializovaných dokumentů JSON a má následující syntaxi, další podrobnosti naleznete v [dokumentaci k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Metoda importAll přijímá následující parametry:
 
   |**Parametr**  |**Popis**  |
   |---------|---------|
   |isUpsert    |   Příznak povolit upsert dokumentů. Pokud dokument s daným ID již existuje, aktualizuje se.  |
   |disableAutomatickéIdGeneration     |   Příznak zakázat automatické generování ID. Ve výchozím nastavení je nastavena na hodnotu true.   |
   |maxConcurrencyPerPartitionRange    |  Maximální stupeň souběžnosti na rozsah klíčů oddílu. Výchozí hodnota je 20.  |

   **Definice objektu odpovědi hromadného importu** Výsledek volání rozhraní API hromadného importu obsahuje následující metody get:

   |**Parametr**  |**Popis**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Celkový počet dokumentů, které byly úspěšně importovány z dokumentů dodaných do volání rozhraní API hromadného importu.      |
   |double getTotalRequestUnitsConsumed()   |  Celkový počet jednotek požadavku (RU) spotřebovaných voláním rozhraní API hromadného importu.       |
   |Doba trvání getTotalTimeTaken()   |    Celková doba, kterou volání rozhraní API hromadného importu trvalo dokončení provádění.     |
   |Výjimka seznamu\<> getErrors() |  Získá seznam chyb, pokud některé dokumenty z dávky dodané do hromadného importu volání rozhraní API se nepodařilo vložit.       |
   |Objekt\<seznamu> getBadInputDocuments()  |    Seznam dokumentů chybného formátu, které nebyly úspěšně importovány v volání rozhraní API hromadného importu. Uživatel by měl opravit vrácené dokumenty a opakovat import. Chybně formátované dokumenty zahrnují dokumenty, jejichž hodnota ID není řetězec (null nebo jakýkoli jiný datový typ je považován za neplatný).     |

5. Po připravení aplikace hromadného importu vytvořte nástroj příkazového řádku ze zdroje pomocí příkazu "mvn clean package". Tento příkaz generuje soubor jar v cílové složce:  

   ```java
   mvn clean package
   ```

6. Po vygenerování cílových závislostí můžete vyvolat aplikaci hromadného importu pomocí následujícího příkazu:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB's endpoint>*  -masterKey *<Fill in your Azure Cosmos DB's master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Velkoobjemový importér vytvoří novou databázi a kolekci s názvem databáze, názvem kolekce a hodnotami propustnosti zadanými v souboru App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Data hromadné aktualizace v Azure Cosmos DB

Existující dokumenty můžete aktualizovat pomocí rozhraní BulkUpdateAsync API. V tomto příkladu nastavíte pole Název na novou hodnotu a odeberete pole Popis z existujících dokladů. Úplnou sadu podporovaných operací aktualizace polí naleznete v [dokumentaci k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definuje položky aktualizace spolu s odpovídajícími operacemi aktualizace polí. V tomto příkladu použijete SetUpdateOperation k aktualizaci pole Název a UnsetUpdateOperation k odebrání pole Description ze všech dokumentů. Můžete také provádět další operace, jako je zvýšení pole dokumentu o určitou hodnotu, nabízení určitých hodnot do pole nebo odebrání určité hodnoty z pole pole. Další informace o různých metodách poskytovaných rozhraním API pro hromadnou aktualizaci naleznete v [dokumentaci k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

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

2. Volání updateAll rozhraní API, které generuje náhodné dokumenty, které mají být hromadně importovány do kontejneru Azure Cosmos. Konfigurace příkazového řádku můžete nakonfigurovat tak, aby byly předány v souboru CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Rozhraní API hromadné aktualizace přijímá kolekci položek, které mají být aktualizovány. Každá položka aktualizace určuje seznam operací aktualizace polí, které mají být provedeny na dokumentu označeném ID a hodnotou klíče oddílu. Další podrobnosti naleznete v [dokumentaci](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)k rozhraní API :

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   Metoda updateAll přijímá následující parametry:

   |**Parametr** |**Popis** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Maximální stupeň souběžnosti na rozsah klíčů oddílu. Výchozí hodnota je 20.  |
 
   **Definice objektu odpovědi hromadného importu** Výsledek volání rozhraní API hromadného importu obsahuje následující metody get:

   |**Parametr** |**Popis**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Celkový počet dokumentů, které byly úspěšně aktualizovány z dokumentů dodaných do volání rozhraní API hromadné aktualizace.      |
   |double getTotalRequestUnitsConsumed() |  Celkový počet jednotek požadavku (RU) spotřebovaných voláním rozhraní API hromadné aktualizace.       |
   |Doba trvání getTotalTimeTaken()  |   Celková doba, kterou volání rozhraní API hromadné aktualizace dokončení provádění.      |
   |Výjimka seznamu\<> getErrors()   |       Získá seznam chyb, pokud některé dokumenty z dávky dodané do volání rozhraní API hromadné aktualizace se nepodařilo vložit.      |

3. Po vytvoření aplikace pro hromadnou aktualizaci vytvořte nástroj příkazového řádku ze zdroje pomocí příkazu "mvn clean package". Tento příkaz generuje soubor jar v cílové složce:  

   ```
   mvn clean package
   ```

4. Po vygenerování cílových závislostí můžete vyvolat aplikaci hromadné aktualizace pomocí následujícího příkazu:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB's endpoint>* -masterKey **<Fill in your Azure Cosmos DB's master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Tipy pro zvýšení výkonu 

Při použití knihovny hromadného vykonavatele zvažte následující body pro lepší výkon:

* Pokud máte co nejlepší výkon, spusťte aplikaci z virtuálního počítače Azure ve stejné oblasti jako oblast zápisu účtu Cosmos DB.  
* Pro dosažení vyšší propustnost:  

   * Nastavte velikost haldy JVM na dostatečně velké číslo, aby se zabránilo jakémukoli problému s pamětí při zpracování velkého počtu dokumentů. Doporučená velikost haldy: max (3 GB, 3 * sizeof (všechny dokumenty předané do rozhraní API hromadného importu v jedné dávce)).  
   * K dispozici je doba předběžného zpracování, díky které získáte vyšší propustnost při provádění hromadných operací s velkým počtem dokumentů. Takže pokud chcete importovat 10 000 000 dokumentů, je vhodnější spustit hromadný import 10krát na 10 přeplňované dokumenty o velikosti 1 000 000 než spuštění hromadného importu 100krát na 100 hromadných dokumentech velikosti 100 000.  

* Doporučujeme vytvořit instanci jednoho objektu DocumentBulkExecutor pro celou aplikaci v rámci jednoho virtuálního počítače, který odpovídá konkrétnímu kontejneru Azure Cosmos.  

* Vzhledem k tomu, že spuštění rozhraní API s jednou hromadnou operací spotřebovává velký blok vznětového procesoru a síťových vzpona. K tomu dochází tím, že vytváří více úloh interně, vyhnout se tření více souběžných úloh v rámci procesu aplikace každý provádění hromadných volání rozhraní API operace. Pokud volání rozhraní API s jednou hromadnou operací spuštěné na jednom virtuálním počítači nemůže spotřebovávat propustnost celého kontejneru (pokud propustnost vašeho kontejneru > 1 milion RU/s), je vhodnější vytvořit samostatné virtuální počítače pro souběžné spuštění hromadných volání rozhraní API.

    
## <a name="next-steps"></a>Další kroky
* Informace o podrobnostech balíčku maven a poznámkách k verzi knihovny Java hromadného vykonavatele naleznete v[tématu hromadné vykonavatelé sady SDK](sql-api-sdk-bulk-executor-java.md).


