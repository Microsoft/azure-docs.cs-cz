---
title: 'Azure Cosmos DB: Příklady Async Javu pro rozhraní SQL API'
description: Vyhledejte si příklady v asynchronní Javě na GitHubu pro běžné úlohy prováděné pomocí rozhraní SQL API služby Azure Cosmos DB, včetně operací CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: sample
ms.date: 06/18/2018
ms.author: sngun
ms.openlocfilehash: 34551dcc64e6add2baa1ec94067037c98de67abe
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917936"
---
# <a name="azure-cosmos-db-async-java-examples-for-the-sql-api"></a>Azure Cosmos DB: Příklady Async Javu pro rozhraní SQL API

> [!div class="op_single_selector"]
> * [Příklady v .NET](sql-api-dotnet-samples.md)
> * [Příkladů v jazyce Java](sql-api-java-samples.md)
> * [Příklady Async Javu](sql-api-async-java-samples.md)
> * [Příklady Node.js](sql-api-nodejs-samples.md)
> * [Příklady Pythonu](sql-api-python-samples.md)
> * [Galerie ukázek kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Nejnovější ukázkové aplikace, které provádí operace CRUD a další běžné operace s prostředky služby Azure Cosmos DB, jsou součástí úložiště [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java) na GitHubu. Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v asynchronní Javě 
* Odkazy na související referenční obsah rozhraní API

**Požadavky**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Je možné [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Vaše předplatné sady Visual Studio vám dává kredity každý měsíc, můžete použít k placení za služby Azure.

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

**Spuštění ukázkové aplikace**

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
   > Každá ukázka je samostatná, sama se nastaví a po ukončení příkladu vyčistí svoje prostředky. Ukázky provedou několik volání metody [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection). Pokaždé, když k tomu dojde, bude pro vaše předplatné účtována 1 hodina využití pro úroveň výkonu vytvořené kolekce. 
   > 
   > 

## <a name="database-examples"></a>Příklady pro databáze
[DatabaseCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java) soubor ukazuje, jak provádět následující úlohy. Další informace o databázi Azure Cosmos před spuštěním následující ukázky, naleznete v tématu [práci s databází, kontejnerů a položky](databases-containers-items.md) konceptuální článek. 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L115-L132) | [AsyncDocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdatabase) |
| [Nepovedlo se vytvořit databázi, která již existuje.](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L189-L204) | |
| [Vytvoření a čtení databáze](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L228-L249) | [AsyncDocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdatabase) |
| [Vytvářet a odstraňovat databáze](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L255-L276) | [AsyncDocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedatabase) |
| [Vytvoření a dotazování databáze](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L282-L312) | [AsyncDocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydatabases) |

## <a name="collection-examples"></a>Příklady pro kolekce
[CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) soubor ukazuje, jak provádět následující úlohy. Další informace o kolekcích Azure Cosmos před spuštěním následující ukázky, viz [práci s databází, kontejnerů a položky](databases-containers-items.md) konceptuální článek. 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce jednoho oddílu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L134-L153) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection) |
| [Vytvoření vlastní kolekce více oddílů](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L164-L184) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions) |
| [Nepovedlo se vytvořit kolekci, která již existuje.](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L241-L256) | |
| [Vytvoření a čtení kolekce](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L281-L304) | [AsyncDocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readcollection) |
| [Vytvářet a odstraňovat kolekce](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L310-L333) | [AsyncDocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletecollection) |
| [Vytvoření a dotazování na kolekci](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L339-L372) | [AsyncDocumentClient.queryCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querycollections) |

## <a name="document-examples"></a>Příklady pro dokumenty
[DocumentCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java) soubor ukazuje, jak provádět následující úlohy. Další informace o dokumenty Azure Cosmos před spuštěním následující ukázky, viz [práci s databází, kontejnerů a položky](databases-containers-items.md) konceptuální článek. 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L137-L156) | [AsyncDocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdocument) |
| [Vytvoření dokumentu s definicí programovatelný dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L213-L242) | [Dokument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resource.setid) |
| [Vytváření dokumentů a najít celkové náklady na RU](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L248-L280) | [ResourceResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resourceresponse.getrequestcharge) |
| [Nepovedlo se vytvořit dokument, který již existuje.](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L316-L336) | |
| [Vytvoření a nahrazení dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L342-L365) | [AsyncDocumentClient.replaceDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replacedocument) |
| [Vytvoření a upsert dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L371-L393) | [AsyncDocumentClient.upsertDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.upsertdocument) |
| [Vytvoření a odstranění dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L399-L431) | [AsyncDocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedocument) |
| [Vytvoření a čtení dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L437-L458) | [AsyncDocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdocument) |

## <a name="indexing-examples"></a>Příklady pro indexování
[CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) soubor ukazuje, jak provádět následující úlohy.  Další informace o indexování ve službě Azure Cosmos DB před spuštěním následujících ukázkách najdete v tématu [zásadám indexování](index-policy.md), [indexování typů](index-types.md), a [indexování cesty](index-paths.md) koncepčních článků. 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření indexu a sada zásady indexování](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L394-L410) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.index)<br>[indexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.indexingpolicy) |

Další informace o indexování najdete v tématu pojednávajícím o [zásadách indexování služby Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Příklady dotazů
[DocumentQuerySamples](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java) soubor ukazuje, jak provádět následující úlohy. Další informace o odkaz na dotaz SQL ve službě Azure Cosmos DB před spuštěním následující ukázky, viz [Příklady dotazů SQL](how-to-sql-query.md) konceptuální článek. 

| Úkol | API – referenční informace |
| --- | --- |
| [Provést dotaz na jednoduché dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L154-L190) | [AsyncDocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments) |
| [Provést dotaz na jednoduché dokumentu a najít náklady na celkový počet RU](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L249-L268) | [FeedResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedresponse.getrequestcharge) |
| [Provést dotaz na jednoduché dokumentu, přečtěte si jednu stránku a zrušit její odběr vrácené pozorovat](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L274-L312) | |
| [Provedení jednoduchého dokumentu dotazu a filtrování výsledků](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L318-L368) | |
| [Provedení dotazu napříč oddíly dokumentu klauzule order by](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L410-L457) | [FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedoptions.setenablecrosspartitionquery) |

Další informace o psaní dotazů najdete v tématu pojednávajícím o [dotazu SQL ve službě Azure Cosmos DB](how-to-sql-query.md).

## <a name="offer-examples"></a>Příklady pro nabídky
Soubor [OfferCRUDAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java) ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce a nastavte propustnost](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L106-L113) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions.setofferthroughput) |
| [Přečtěte si kolekci najít související nabídky](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L118-L130) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getContent)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.queryoffers) |
| [Aktualizovat tak, že nahradíte jeho nabídky propustnosti kolekci](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L101-L153) | [AsyncDocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>Příklady pro uložené procedury
[StoredProcedureAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java) soubor ukazuje, jak provádět následující úlohy. Další informace o programování na straně serveru ve službě Azure Cosmos DB před spuštěním následujících ukázkách najdete v tématu [uložené procedury, triggery a uživatelem definovaných funkcí](stored-procedures-triggers-udfs.md) konceptuální článek. 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření a spuštění uložené procedury](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L108-L155) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createstoredprocedure)<br>[AsyncDocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.executestoredprocedure) |
| [Vytvoření a spuštění uložené procedury s argumenty](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L161-L195) | |
| [Vytvoření a spuštění uložené procedury s parametrem objektu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L201-L241) | |

## <a name="unique-key"></a>Jedinečný klíč
[UniqueIndexAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java) soubor ukazuje, jak provádět následující úlohy. Další informace o jedinečných klíčů ve službě Azure Cosmos DB před spuštěním následující ukázky, viz [omezení jedinečných klíčů](unique-keys.md) konceptuální článek. 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvořte kolekci s jedinečný klíč](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java#L65-L97) | [UniqueKey](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekey)<br>[UniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekeypolicy)<br>[DocumentCollection.setUniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection.setuniquekeypolicy) |
