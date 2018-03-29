---
title: Příklady Python SQL rozhraní API pro Azure Cosmos DB | Microsoft Docs
description: Najít příklady Python pro běžné úlohy v Azure DB Cosmos, včetně operace CRUD na githubu.
keywords: Příklady Python
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: python
ms.assetid: 7f4f8db3-e9db-4645-92ef-7819d486a349
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 03/14/2018
ms.author: mimig
ms.openlocfilehash: ce9a7d071575b30e0150b23291292bddd0c65f1f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python příklady

> [!div class="op_single_selector"]
> * [Příklady rozhraní .NET](sql-api-dotnet-samples.md)
> * [Příklady Java](sql-api-java-samples.md)
> * [Příklady Node.js](sql-api-nodejs-samples.md)
> * [Příklady Python](sql-api-python-samples.md)
> * [Galerie ukázkový kód Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Ukázka řešení, která provádět operace CRUD a dalších běžných operací s prostředky Azure Cosmos DB jsou součástí [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) úložiště GitHub. Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých souborů projektu příklad Python. 
* Odkazy na související rozhraní API odkazovat na obsah.

**Požadavky**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Můžete [aktivovat výhody předplatitele Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Visual Studio vaše předplatné vám dává kredity každý měsíc, které můžete použít pro placené služby Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Musíte taky [Python SDK](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Každá ukázka je samostatný; Nastaví samotné a vyčistí po sám sebe. Ukázky vydat několik volání [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Pokaždé, když toto dokončíte, vaše předplatné se fakturuje hodinu využití. Další informace o fakturaci Azure Cosmos DB najdete v tématu [Azure Cosmos DB ceny](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Příklady databáze
[Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) soubor [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) projektu ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) |[document_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDatabase) |
| [Čtení databáze podle ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) |[document_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDatabase) |
| [Seznam databází pro účet](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) |[document_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Odstranění databáze](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) |[document_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |

## <a name="collection-examples"></a>Příklady kolekce
[Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) soubor [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) projektu ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) |[document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Přečtěte si seznam všech kolekcí v databázi](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L209) |[document_client.ReadCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadCollections) |
| [Získání kolekce podle ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) |[document_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadCollection) |
| [Změnit propustnost kolekce](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L168-L172) | [document_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)|
| [Odstranit kolekci](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) |[document_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#DeleteCollection) |

## <a name="document-examples"></a>Příklady dokumentu
[Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) soubor [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) projektu ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření dokumentu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocument](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDocument) |
| [Vytvoření kolekce dokumentů](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDocuments) |
| [Čtení dokumentu podle ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[document_client.ReadDocument](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDocument) |
| [Číst všechny dokumenty v kolekci](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[document_client.ReadDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDocuments) |

## <a name="indexing-examples"></a>Příklady indexování
[Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) soubor [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) projektu ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Použít indexování ruční (namísto Automatická)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) |[IndexingPolicy.Automatic](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Vyloučit z indexu zadaný dokument cesty](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) |[IndexingPolicy.ExcludedPaths](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Vyloučit dokumentu z indexu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[documents.IndexingDirective.Exclude](http://azure.github.io/azure-documentdb-python/_modules/pydocumentdb/documents.html#IndexingDirective) |
| [Nastavit režim indexování](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[documents.IndexingMode](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.documents.html#IndexingMode) |
| [Použít rozsah indexy na řetězce](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) |[IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Proveďte transformaci indexu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[document_client.ReplaceCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReplaceCollection) |

## <a name="query-examples"></a>Příklady dotazů
Ukázkové projekty také ukazují, jak provádět následující úlohy dotazu:

| Úkol | API – referenční informace |
| --- | --- |
| [Dotaz na účet pro databázi](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) |[document_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Dotaz pro dokumenty](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[document_client.QueryDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#QueryDocuments) |
| [Vynutit operaci prohledávání rozsah v cestě indexované hash](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[http_constants.HttpHeaders.EnableScanInQuery](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.http_constants.html#pydocumentdb.http_constants.HttpHeaders.EnableScanInQuery) |
