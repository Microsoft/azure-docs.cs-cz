---
title: 'Azure Cosmos DB: Příklady Java rozhraní SQL API | Microsoft Docs'
description: Pro běžné úlohy pomocí Cosmos DB SQL rozhraní API služby Azure, včetně operace CRUD najít příkladů Java na Githubu.
keywords: Příklad NoSQL
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: java
ms.assetid: d824d517-903e-4d82-ab0a-09fc3b984c84
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/08/2018
ms.author: sngun
ms.openlocfilehash: 270cebd08fb1b03851825f7678689598cb9b71b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB: Příklady Java rozhraní SQL API

> [!div class="op_single_selector"]
> * [Příklady rozhraní .NET](sql-api-dotnet-samples.md)
> * [Příklady Java](sql-api-java-samples.md)
> * [Příklady Node.js](sql-api-nodejs-samples.md)
> * [Příklady Python](sql-api-python-samples.md)
> * [Galerie ukázkový kód Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Nejnovější ukázkové aplikace, které provádějí operace CRUD a dalších běžných operací s prostředky Azure Cosmos DB jsou součástí [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) úložiště GitHub. Tento článek obsahuje:

* Soubory projektu odkazy na úlohy v každé z příkladu Java. 
* Odkazy na související rozhraní API odkazovat na obsah.

**Požadavky**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Můžete [aktivovat výhody předplatitele Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Visual Studio vaše předplatné vám dává kredity každý měsíc, které můžete použít pro placené služby Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Budete potřebovat ke spuštění této ukázkové aplikaci:

* Java Development Kit 7
* Microsoft Azure DocumentDB Java SDK

Volitelně můžete Maven získat nejnovější binární soubory sady Microsoft Azure DocumentDB Java SDK pro použití ve vašem projektu. Maven automaticky přidá všechny potřebné závislosti. Můžete, jinak hodnota přímo stáhnout závislostí uvedených v soubor pom.xml a přidat je do cestu k sestavení.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**Spuštění ukázkové aplikace**

Naklonujte úložiště ukázka:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

Můžete spustit ukázky buď pomocí Eclipse nebo z příkazového řádku pomocí nástroje Maven.

Spuštění z prostředí Eclipse:
* Načíst soubor pom.xml hlavní nadřazený projekt v prostředí Eclipse; automaticky se by se měly načíst příklady documentdb.
* Spustit ukázky, budete potřebovat platný koncový bod Azure DB Cosmos. Koncové body se načítají z `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java`.
* Přihlašovací údaje koncového bodu může předat jako argumenty virtuálních počítačů v prostředí Eclipse JUnit spustit konfiguraci nebo můžete vložit přihlašovacích údajů koncového bodu v AccountCredentials.java.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Nyní můžete spustit ukázky jako JUnit testy v prostředí Eclipse.

Spuštění z příkazového řádku:
* Druhý způsob ke spuštění ukázky je s použitím nástroje maven:
* Spusťte Maven a předat přihlašovacích údajů Azure Cosmos DB koncový bod:
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > Každá ukázka je samostatný; Nastaví samotné a vyčistí po sám sebe. Ukázky vydat několik volání [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection). Pokaždé, když toto dokončíte, vaše předplatné se fakturuje 1 hodinu využití pro úroveň výkonu kolekce vytvořili. 
   > 
   > 

## <a name="database-examples"></a>Příklady databáze
[DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) souboru ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření a čtení databáze](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource.setid) |
| [Vytvářet a odstraňovat databáze](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedatabase) |
| [Vytvoření a dotaz databáze](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydatabases) |

## <a name="collection-examples"></a>Příklady kolekce
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) souboru ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce tvořené jedním oddílem](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Vytvoření vlastní kolekce více oddílů](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._partition_key_definition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._request_options) |
| [Odstranit kolekci](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletecollection) |

## <a name="document-examples"></a>Příklady dokumentu
[DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) souboru ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytváření, čtení a odstranění dokumentu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedocument) |
| [Vytvořte dokument s definicí programovatelný dokumentu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [Dokument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource.setid) |

## <a name="indexing-examples"></a>Příklady indexování
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) souboru ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvořte index a sadu indexování zásad](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy) |

Další informace o indexování najdete v tématu [Azure DB Cosmos indexování zásady](indexing-policies.md).

## <a name="query-examples"></a>Příklady dotazů
[DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) souboru ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Provést dotaz na jednoduchý cross-partition dokumentu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setenablecrosspartitionquery) |
| [Řadit podle dotazu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response.getqueryiterator) |

Další informace o vytváření dotazů najdete v tématu [dotazu SQL v Azure Cosmos DB](sql-api-sql-query.md).

## <a name="offer-examples"></a>Příklady nabídka
[OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) souboru ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce a nastavte propustnost](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection)<br>[RequestOptions.setOfferThroughput ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._request_options.setofferthroughput) |
| [Přečíst kolekce najít související nabídky](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.queryoffers) |

## <a name="partition-key-examples"></a>Příklady klíče oddílu
[SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) souboru ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce tvořené jedním oddílem](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Změnit nabídku propustnost pro kolekce tvořené jedním oddílem](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer) |

## <a name="stored-procedure-examples"></a>Příklady uložené procedury
[StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) souboru ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření uložené procedury](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._stored_procedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createstoredprocedure) |
| [Spuštění uložené procedury s argumenty](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
| [Spuštění uložené procedury s parametrem objektu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
