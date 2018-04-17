---
title: Příklady Node.js pro Azure Cosmos DB | Microsoft Docs
description: Pro běžné úlohy v Azure DB Cosmos, včetně operace CRUD najít příklady Node.js na githubu.
keywords: Příklady Node.js
services: cosmos-db
author: moderakh
manager: kfile
editor: monicar
documentationcenter: nodejs
ms.assetid: d87d97be-47a5-4928-8d46-a541fbb33213
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: moderakh
ms.openlocfilehash: 3d18e9bde7c8e5a5b8c3f192678f62eedf5fb693
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Příklady Azure Cosmos DB Node.js
> [!div class="op_single_selector"]
> * [Příklady rozhraní .NET](sql-api-dotnet-samples.md)
> * [Příklady Node.js](sql-api-nodejs-samples.md)
> * [Příklady Python](sql-api-python-samples.md)
> * [Galerie ukázkový kód Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Ukázka řešení, která provádět operace CRUD a dalších běžných operací s prostředky Azure Cosmos DB jsou součástí [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) úložiště GitHub. Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých souborů projektu příklad Node.js.
* Odkazy na související rozhraní API odkazovat na obsah.

**Požadavky**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Můžete [aktivovat výhody předplatitele Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Visual Studio vaše předplatné vám dává kredity každý měsíc, které můžete použít pro placené služby Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Musíte taky [Node.js SDK](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Každá ukázka je samostatný, nastaví sám a vyčistí po sám sebe. Jako takový ukázky vydat několik volání [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection). Pokaždé, když k tomu je vaše předplatné bude účtován na 1 hodinu využití na úroveň výkonu kolekce vytváří.
   > 
   > 

## <a name="database-examples"></a>Příklady databáze
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) soubor [DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) projektu ukazuje, jak provádět následující úlohy.

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) |[DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase) |
| [Dotaz na účet pro databázi](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) |[DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabases) |
| [Čtení databáze podle Id](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) |[DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase) |
| [Seznam databází pro účet](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) |[DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabases) |
| [Odstranění databáze](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) |[DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase) |

## <a name="collection-examples"></a>Příklady kolekce
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) soubor [CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) projektu ukazuje, jak provádět následující úlohy.

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection) |
| [Přečtěte si seznam všech kolekcí v databázi](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) |[DocumentClient.readCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollections) |
| [Získat kolekci _self](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) |[DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) |
| [Získání kolekce podle Id](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) |[DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) |
| [Získat úroveň výkonu kolekce](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) |[DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers) |
| [Změnit úroveň výkonu kolekce](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) |[DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer) |
| [Odstranit kolekci](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) |[DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection) |

## <a name="document-examples"></a>Příklady dokumentu
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) soubor [DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) projektu ukazuje, jak provádět následující úlohy.

| Úkol | API – referenční informace |
| --- | --- |
| [Vytváření dokumentů](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) |[DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument) |
| [Čtení dokumentu kanálu pro kolekci](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument) |
| [Čtení dokumentu podle ID](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument) |
| [Dokument číst pouze v případě, že došlo ke změně dokumentu](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Dotaz pro dokumenty](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) |[DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments) |
| [Nahrazení dokumentu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |[DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument) |
| [Nahraďte podmíněného ETag Kontrola dokumentu](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |[DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Odstranění dokumentu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) |[DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument) |

## <a name="indexing-examples"></a>Příklady indexování
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) soubor [IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) projektu ukazuje, jak provádět následující úlohy.

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvořte kolekci s výchozí indexování](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L657-L701) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection) |
| [Ručně index určitého dokumentu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) |[RequestOptions.indexingDirective: "zahrnovat"](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Ručně vyloučit konkrétní dokumentu z indexu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) |[RequestOptions.indexingDirective: vyloučení](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Použít Opožděné indexování pro hromadný import nebo číst velkou kolekce](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) |[IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#IndexingMode) |
| [Indexování zahrnout konkrétních cest dokumentu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) |[IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy) |
| [Vyloučení určitých cest z indexování](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) |[IndexingPolicy.ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy) |
| [Povolit kontrolu v cestě řetězec během operace rozsahu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347) |[FeedOptions.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions) |
| [Vytvořte index rozsah v cestě řetězec](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) |[IndexKind.Range](http://azure.github.io/azure-documentdb-node/global.html#IndexKind), [IndexingPolicy](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy), [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument) |
| [Vytvořte kolekci s výchozí indexPolicy a potom aktualizovat online](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html) |

Další informace o indexování najdete v tématu [Azure DB Cosmos indexování zásady](indexing-policies.md).

## <a name="server-side-programming-examples"></a>Ukázky programování na straně serveru
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) soubor [ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) projektu ukazuje, jak provádět následující úlohy.

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření uložené procedury](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) |[DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure) |
| [Provedení uložené procedury](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) |[DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure) |

Další informace o programování na straně serveru najdete v tématu [programování na straně serveru Azure Cosmos DB: uložené procedury, triggery databáze a UDF](programming.md).

## <a name="partitioning-examples"></a>Vytváření oddílů příklady
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) soubor [dělení](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) projektu ukazuje, jak provádět následující úlohy.

| Úkol | API – referenční informace |
| --- | --- |
| [Použít HashPartitionResolver](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) |[HashPartitionResolver](http://azure.github.io/azure-documentdb-node/HashPartitionResolver.html) |

Další informace o vytváření oddílů dat v Azure Cosmos DB najdete v tématu [oddílu a škálování dat v Azure Cosmos DB](partition-data.md).

