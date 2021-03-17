---
title: 'Azure Cosmos DB SQL API: příklady Java SDK v4'
description: Vyhledejte si příklady v Javě na GitHubu pro běžné úlohy prováděné pomocí rozhraní SQL API služby Azure Cosmos DB, včetně operací CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 0b0bdd204b2aa322adeb7373911b90e742c71999
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018985"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API: příklady Java SDK v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Příklady sady .NET v2 SDK](sql-api-dotnet-samples.md)
> * [Příklady sady .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Příklady sady Java v4 SDK](sql-api-java-sdk-samples.md)
> * [Příklady sad data pružiny V3 SDK](sql-api-spring-data-sdk-samples.md)
> * [Příklady v Node.js](sql-api-nodejs-samples.md)
> * [Příklady v Pythonu](sql-api-python-samples.md)
> * [Galerie ukázkového kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Další informace o sadě Java SDK v4 najdete v tématu Azure Cosmos DB zpráva k [vydání verze](sql-api-sdk-java-v4.md)Java SDK v4, [úložiště pro Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java sdk v4 – [tipy](performance-tips-java-sdk-v4-sql.md)Azure Cosmos DB a [Průvodce odstraňováním potíží](troubleshoot-java-sdk-v4-sql.md) se sadou Java SDK v4. Pokud aktuálně používáte starší verzi než v4, přečtěte si článek průvodce [migrací do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , který vám pomůže s upgradem na V4.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Můžete si [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Za své předplatné sady Visual Studio každý měsíc získáváte kredity, které můžete použít k placení za služby Azure.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Nejnovější ukázkové aplikace, které provádějí operace CRUD a další běžné operace týkající se Azure Cosmos DBch prostředků, jsou součástí úložiště GitHub [Azure-Cosmos-Java-SQL-API-Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) . Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v Javě. 
* Odkazy na související referenční obsah rozhraní API.

**Požadavky**

Ke spuštění této ukázkové aplikace budete potřebovat:

* Sadu Java Development Kit (JDK) 8
* Azure Cosmos DB Java SDK v4

Volitelně můžete použít Maven a získat nejnovější Azure Cosmos DB binárních souborů Java SDK v4 pro použití ve vašem projektu. Maven automaticky přidá všechny potřebné závislosti. Můžete taky přímo stáhnout závislosti uvedené v souboru pom.xml a přidat je do cesty sestavení.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Spuštění ukázkových aplikací**

Naklonování ukázkového úložiště:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

Ukázky můžete spustit buď pomocí integrovaného vývojového prostředí (zatmění, IntelliJ nebo VSCODE), nebo z příkazového řádku pomocí Maven.

Tyto proměnné prostředí musí být nastavené.

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account primary key
```

aby se k vašemu účtu daly získat přístup ke čtení a zápisu těchto ukázek.

Chcete-li spustit ukázku, zadejte její hlavní třídu. 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

kde může být *Sample. synchronicity. MainClass*
* crudquickstart. Sync. SampleCRUDQuickstart
* crudquickstart. Async. SampleCRUDQuickstartAsync
* indexmanagement. Sync. SampleIndexManagement
* indexmanagement. Async. SampleIndexManagementAsync
* StoredProcedure. Sync. SampleStoredProcedure
* StoredProcedure. Async. SampleStoredProcedureAsync
* changefeed. SampleChangeFeedProcessor *(Changefeed má pouze asynchronní vzorek bez synchronní ukázky.)* ... atd...

> [!NOTE]
> Každá ukázka je samostatná, sama se nastaví a po ukončení příkladu vyčistí svoje prostředky. Ukázky vydávají více volání pro vytvoření `CosmosContainer` . Pokaždé, když k tomu dojde, bude pro vaše předplatné účtována 1 hodina využití pro úroveň výkonu vytvořené kolekce. 
> 
> 

## <a name="database-examples"></a>Příklady pro databáze
Soubor [ukázek pro databázi CRUD](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) ukazuje, jak provádět následující úlohy. Další informace o databázích Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md) konceptu. 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L77-L85) | CosmosClient. metodu createdatabaseifnotexists |
| [Čtení databáze podle ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L88-L95) | CosmosClient. getdatabase |
| [Čtení všech databází](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L98-L112) | CosmosClient.readAllDatabases |
| [Odstranění databáze](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L115-L123) | CosmosDatabase. Delete |

## <a name="collection-examples"></a>Příklady pro kolekce
Soubor [ukázek pro CRUD kolekce](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) ukazuje, jak provádět následující úlohy. Informace o kolekcích Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md) konceptu.

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření kolekce](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L97-L112) | CosmosDatabase. metodu createcontainerifnotexists |
| [Změna konfigurovaného výkonu kolekce](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L115-L123) | CosmosContainer.replaceProvisionedThroughput |
| [Získat kolekci podle ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L126-L133) | CosmosDatabase. Get– kontejner |
| [Čtení všech kolekcí v databázi](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L136-L150) | CosmosDatabase.readAllContainers |
| [Odstranění kolekce](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L153-L161) | CosmosContainer. Delete |

## <a name="autoscale-collection-examples"></a>Příklady kolekce automatického škálování

Další informace o automatickém škálování před spuštěním těchto ukázek najdete v těchto pokynech k povolení automatického škálování ve vašem [účtu](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) a ve vašich [databázích a kontejnerech](./provision-throughput-autoscale.md).

Soubor [vzorků s ukázkami databáze pro databázi automatického škálování](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) ukazuje, jak provádět následující úlohy.

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření databáze se zadaným maximální propustností automatického škálování](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L78-L89) | CosmosClient. createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

Soubor [vzorků s ukázkami funkce CRUD kolekce automatického škálování](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) ukazuje, jak provádět následující úlohy. 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření kolekce se zadaným maximální propustností automatického škálování](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L97-L110) | CosmosDatabase. metodu createcontainerifnotexists |
| [Změna konfigurované propustnosti pro automatické škálování v kolekci](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L113-L120) | CosmosContainer.replaceThroughput |
| [Čtení konfigurace propustnosti v kolekci automatického škálování](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L122-L133) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Příklady shromažďování analytického úložiště

Soubor [vzorků s ukázkami pro analýzu analytického úložiště](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) ukazuje, jak provádět následující úlohy. Informace o kolekcích Azure Cosmos před spuštěním následujících ukázek najdete v článku o Azure Cosmos DB synapse a analytickém úložišti.

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření kolekce](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L93-L108) | CosmosDatabase. metodu createcontainerifnotexists |

## <a name="document-examples"></a>Příklady pro dokumenty
Soubor [ukázek dokumentů CRUD](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) ukazuje, jak provádět následující úlohy. Další informace o dokumentech Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md) konceptu.

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L133-L147) | CosmosContainer. CreateItem – |
| [Čtení dokumentu podle ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L179-L193) | CosmosContainer.readItem |
| [Dotaz na dokumenty](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176) | CosmosContainer.queryItems |
| [Nahrazení dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L195-L210) | CosmosContainer.replaceItem |
| [Upsert dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L212-L2225) | CosmosContainer.upsertItem |
| [Odstranění dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L303-L310) | CosmosContainer.deleteItem |
| [Nahrazení dokumentu podmíněnou kontrolou ETag](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L227-L264) | AccessCondition.setType<br>AccessCondition.setCondition |
| [Čtení dokumentu jenom v případě, že došlo k jeho změně](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L266-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Příklady indexování
Soubor [ukázek pro CRUD kolekce](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) ukazuje, jak provádět následující úlohy. Další informace o indexování v Azure Cosmos DB před spuštěním následujících ukázek najdete v článku o [zásadách indexování](index-policy.md), [typech indexování](index-overview.md#index-types)a [indexovaných cestách](index-policy.md#include-exclude-paths) . 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| Vyloučení dokumentu z indexu | ExcludedIndex<br>IndexingPolicy |
| Použití opožděného indexování | IndexingPolicy.IndexingMode |
| [Zahrnout zadané cesty dokumentů do indexu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L145-L148) | IndexingPolicy.IncludedPaths |
| [Vyloučit zadané cesty dokumentů z indexu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L150-L153) | IndexingPolicy.ExcludedPaths |
| [Vytvoření složeného indexu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L171-L186) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Vynucení operace prohledávání rozsahu v cestě s indexem hash | FeedOptions.EnableScanInQuery |
| Použít indexy rozsahu u řetězců | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Provedení transformace indexu | - |
| [Vytvoření geoprostorového indexu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L157-L166) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

Další informace o indexování najdete v tématu pojednávajícím o [zásadách indexování služby Azure DB Cosmos](index-policy.md).

## <a name="query-examples"></a>Příklady dotazů
V souboru [ukázek dotazů](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) se dozvíte, jak provádět následující úlohy pomocí gramatiky dotazů SQL. Další informace o odkazech na dotaz SQL v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [příklady dotazů SQL pro Azure Cosmos DB](./sql-query-getting-started.md). 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Dotazování na všechny dokumenty](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L210-L214) | CosmosContainer.queryItems |
| [Dotaz na rovnost s využitím ==](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L291-L295) | CosmosContainer.queryItems |
| [Dotaz na nerovnost s využitím != a NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L297-L305) | CosmosContainer.queryItems |
| [Dotaz s využitím operátorů rozsahu jako >, <, >=, <=](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L307-L312) | CosmosContainer.queryItems |
| [Dotaz s využitím operátorů rozsahu na řetězce](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L314-L319) | CosmosContainer.queryItems |
| [Dotaz s ŘAZENÍm podle](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L321-L326) | CosmosContainer.queryItems |
| [Dotaz s JEDINEČNÝm](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L328-L333) | CosmosContainer.queryItems |
| [Dotaz s agregačními funkcemi](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L335-L343) | CosmosContainer.queryItems |
| [Práce s vnořenými dokumenty](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L345-L353) | CosmosContainer.queryItems |
| [Dotaz s využitím operace Join uvnitř dokumentů](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L355-L377) | CosmosContainer.queryItems |
| [Dotaz pomocí operátorů String, Math a Array](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L379-L390) | CosmosContainer.queryItems |
| [Dotaz pomocí parametrizovaného SQL s využitím SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421) |CosmosContainer.queryItems |
| [Dotaz s využitím explicitního stránkování](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216-L266) | CosmosContainer.queryItems |
| [Paralelní dotazování dělených kolekcí](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L268-L289) | CosmosContainer.queryItems |
| Dotaz s ŘAZENÍm podle oddílů pro dělené kolekce | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Příklady kanálů změn 
[Ukázkový soubor procesoru Change feed](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) ukazuje, jak provádět následující úlohy. Další informace o kanálu změn v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [čtení Azure Cosmos DB Změna kanálu](read-change-feed.md) a [Změna procesoru kanálu](change-feed-processor.md).

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Základní funkce změny kanálu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Číst kanál změn z konkrétního času | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Číst kanál změn od začátku](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) | - |

## <a name="server-side-programming-examples"></a>Příklady programování na straně serveru

[Ukázkový soubor uložené procedury](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) ukazuje, jak provádět následující úlohy. Další informace o programování na straně serveru v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [uložené procedury, triggery a uživatelsky definované funkce](stored-procedures-triggers-udfs.md).

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření uložené procedury](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L132-L151) | CosmosScripts.createStoredProcedure |
| [Spuštění uložené procedury](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L167-L181) | CosmosStoredProcedure.exeroztomilá |
| [Odstraní uloženou proceduru.](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L183-L193) | CosmosStoredProcedure. Delete |

## <a name="user-management-examples"></a>Příklady správy uživatelů
Ukázkový soubor správy uživatelů ukazuje, jak provádět následující úlohy:

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| Vytvoření uživatele | - |
| Nastavení oprávnění pro kolekci nebo dokument | - |
| Získání seznamu uživatelských oprávnění |- |
