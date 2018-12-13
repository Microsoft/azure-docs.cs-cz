---
title: 'Azure Cosmos DB: Příklady v asynchronní Javě pro rozhraní SQL API'
description: Vyhledejte si příklady v asynchronní Javě na GitHubu pro běžné úlohy prováděné pomocí rozhraní SQL API služby Azure Cosmos DB, včetně operací CRUD.
services: cosmos-db
author: SnehaGunda
documentationcenter: java
ms.service: cosmos-db
ms.devlang: java
ms.topic: sample
ms.date: 06/18/2018
ms.author: sngun
ms.openlocfilehash: 4c3f731695c21f85c1ceea908e0aee5cd340d3b2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52880059"
---
# <a name="azure-cosmos-db-async-java-examples-for-the-sql-api"></a>Azure Cosmos DB: Příklady v asynchronní Javě pro rozhraní SQL API

> [!div class="op_single_selector"]
> * [Příklady v .NET](sql-api-dotnet-samples.md)
> * [Příklady v Javě](sql-api-java-samples.md)
> * [Příklady v asynchronní Javě](sql-api-async-java-samples.md)
> * [Příklady v Node.js](sql-api-nodejs-samples.md)
> * [Příklady v Pythonu](sql-api-python-samples.md)
> * [Galerie ukázkového kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Nejnovější ukázkové aplikace, které provádí operace CRUD a další běžné operace s prostředky služby Azure Cosmos DB, jsou součástí úložiště [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java) na GitHubu. Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v asynchronní Javě 
* Odkazy na související referenční obsah rozhraní API

**Požadavky**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Můžete si [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Za své předplatné sady Visual Studio každý měsíc získáváte kredity, které můžete použít k placení za služby Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Ke spuštění této ukázkové aplikace budete potřebovat:

* Sadu Java Development Kit (JDK) 8
* Sadu Microsoft Azure Cosmos DB Java SDK

Volitelně můžete použít Maven k získání nejnovějších binárních souborů sady Microsoft Azure Cosmos DB Java SDK pro použití ve vašem projektu. Nejnovější verzi si můžete vybrat [tady](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Maven automaticky přidá všechny potřebné závislosti. Můžete taky přímo stáhnout závislosti uvedené v souboru pom.xml a přidat je do cesty sestavení.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-cosmosdb</artifactId>
    <version>${LATEST}</version>
</dependency>
```

**Spuštění ukázkových aplikací**

Naklonování ukázkového úložiště:
```bash
$ git clone https://github.com/Azure/azure-cosmosdb-java.git

$ cd azure-cosmosdb-java
```

Ukázky můžete spustit buď pomocí Eclipse nebo z příkazového řádku pomocí Mavenu.

Spuštění z Eclipse:
* Načtěte soubor pom.xml hlavního nadřazeného projektu do Eclipse; měl by automaticky načíst příklady azure cosmosdb.
* Ke spuštění příkladů budete potřebovat platný koncový bod služby Azure Cosmos DB. Koncové body se načítají z `src/test/java/com/microsoft/azure/cosmosdb/rx/examples/TestConfigurations.java`.
* Přihlašovací údaje koncového bodu můžete předat jako argumenty virtuálních počítačů v Eclipse JUnit Run Config nebo můžete přihlašovací údaje koncového bodu vložit do souboru TestConfigurations.java.
    ```bash
    -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```
* Nyní můžete spustit ukázky jako testy JUnit v Eclipse.

Spuštění z příkazového řádku:
* Další způsob, jak můžete spustit ukázky, je použít Maven:
* Spusťte Maven a předejte přihlašovací údaje koncového bodu služby Azure Cosmos DB:
    ```bash
    mvn test -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```

   > [!NOTE]
   > Každá ukázka je samostatná, sama se nastaví a po ukončení příkladu vyčistí svoje prostředky. Ukázky provedou několik volání metody [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.createcollection). Pokaždé, když k tomu dojde, bude pro vaše předplatné účtována 1 hodina využití pro úroveň výkonu vytvořené kolekce. 
   > 
   > 

## <a name="database-examples"></a>Příklady pro databáze
Soubor [DatabaseCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java) ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L115-L132) | [AsyncDocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.createdatabase) |
| [Chyba při vytváření databáze, která už existuje](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L189-L204) | |
| [Vytvoření a čtení databáze](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L228-L249) | [AsyncDocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.readdatabase) |
| [Vytvoření a odstranění databáze](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L255-L276) | [AsyncDocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.deletedatabase) |
| [Vytvoření a dotazování databáze](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L282-L312) | [AsyncDocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydatabases) |

## <a name="collection-examples"></a>Příklady pro kolekce
Soubor [CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce tvořené jedním oddílem](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L134-L153) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.createcollection) |
| [Vytvoření vlastní kolekce s více oddíly](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L164-L184) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._document_collection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._partition_key_definition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._request_options) |
| [Chyba při vytváření kolekce, která už existuje](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L241-L256) | |
| [Vytvoření a čtení kolekce](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L281-L304) | [AsyncDocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.readcollection) |
| [Vytvoření a odstranění kolekce](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L310-L333) | [AsyncDocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.deletecollection) |
| [Vytvoření a dotazování kolekce](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L339-L372) | [AsyncDocumentClient.queryCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querycollections) |

## <a name="document-examples"></a>Příklady pro dokumenty
Soubor [DocumentCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java) ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L137-L156) | [AsyncDocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.createdocument) |
| [Vytvoření dokumentu s programovatelnou definicí dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L213-L242) | [Dokument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._resource.setid) |
| [Vytváření dokumentů a vyhledání celkových nákladů na RU](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L248-L280) | [ResourceResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._resource_response.getrequestcharge) |
| [Chyba při vytváření dokumentu, který už existuje](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L316-L336) | |
| [Vytvoření a nahrazení dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L342-L365) | [AsyncDocumentClient.replaceDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.replacedocument) |
| [Vytvoření a upsertování dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L371-L393) | [AsyncDocumentClient.upsertDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.upsertdocument) |
| [Vytvoření a odstranění dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L399-L431) | [AsyncDocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.deletedocument) |
| [Vytvoření a čtení dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L437-L458) | [AsyncDocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.readdocument) |

## <a name="indexing-examples"></a>Příklady pro indexování
Soubor [CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření indexu a nastavení zásad indexování](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L394-L410) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._indexing_policy) |

Další informace o indexování najdete v tématu pojednávajícím o [zásadách indexování služby Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Příklady dotazů
Soubor [DocumentQuerySamples](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java) ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Provedení jednoduchého dotazu na dokument](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L154-L190) | [AsyncDocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments) |
| [Provedení jednoduchého dotazu na dokument a vyhledání celkových nákladů na RU](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L249-L268) | [FeedResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._feed_response.getrequestcharge) |
| [Provedení jednoduchého dotazu na dokument, přečtení jedné stránky a odhlášení vráceného pozorovatelného odběru](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L274-L312) | |
| [Provedení jednoduchého dotazu na dokument a filtrování výsledků](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L318-L368) | |
| [Provedení seřazeného dotazu na dokument napříč oddíly](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L410-L457) | [FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._feed_options.setenablecrosspartitionquery) |

Další informace o psaní dotazů najdete v tématu pojednávajícím o [dotazu SQL ve službě Azure Cosmos DB](how-to-sql-query.md).

## <a name="offer-examples"></a>Příklady pro nabídky
Soubor [OfferCRUDAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java) ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce a nastavení propustnosti](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L106-L113) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._request_options.setofferthroughput) |
| [Čtení kolekce kvůli nalezení související nabídky](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L118-L130) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._offer.getContent)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.queryoffers) |
| [Aktualizace propustnosti kolekce nahrazením její nabídky](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L101-L153) | [AsyncDocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.replaceoffer) |

## <a name="stored-procedure-examples"></a>Příklady pro uložené procedury
Soubor [StoredProcedureAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java) ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření a spuštění uložené procedury](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L108-L155) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._stored_procedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.createstoredprocedure)<br>[AsyncDocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.executestoredprocedure) |
| [Vytvoření a spuštění uložené procedury s argumenty](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L161-L195) | |
| [Vytvoření a spuštění uložené procedury s argumentem objektu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L201-L241) | |

## <a name="unique-key"></a>Jedinečný klíč
Soubor [UniqueIndexAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java) ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce s jedinečným klíčem](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java#L65-L97) | [UniqueKey](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._unique_key)<br>[UniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._unique_key_policy)<br>[DocumentCollection.setUniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._document_collection.setuniquekeypolicy) |
