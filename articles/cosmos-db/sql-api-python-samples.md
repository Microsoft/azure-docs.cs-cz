---
title: Příklady SQL API Pythonu pro Azure Cosmos DB
description: Příklady v Pythonu najdete na GitHubu pro běžné úlohy v Azure Cosmos DB, včetně operací CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.custom: tracking-python
ms.openlocfilehash: c52651e15f648bede419501f516392acbc266e8a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558012"
---
# <a name="azure-cosmos-db-python-examples"></a>Příklady Azure Cosmos DB Python

> [!div class="op_single_selector"]
> * [Příklady sady .NET v2 SDK](sql-api-dotnet-samples.md)
> * [Příklady sady .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Příklady sady Java v4 SDK](sql-api-java-sdk-samples.md)
> * [Příklady v Node.js](sql-api-nodejs-samples.md)
> * [Příklady v Pythonu](sql-api-python-samples.md)
> * [Galerie ukázkového kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Ukázková řešení, která dělají operace CRUD a další běžné operace týkající se Azure Cosmos DB prostředků, jsou součástí úložiště GitHub [Azure-DocumentDB-Python](https://github.com/Azure/azure-documentdb-python) . Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v Pythonu.
* Odkazy na související referenční obsah rozhraní API.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Můžete si [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Za své předplatné sady Visual Studio každý měsíc získáváte kredity, které můžete použít k placení za služby Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Potřebujete taky [Python SDK](sql-api-sdk-python.md).

   > [!NOTE]
   > Každá ukázka je samostatná, sama se nastaví a po ukončení příkladu vyčistí svoje prostředky. Ukázky nastavují více volání `CosmosClient.CreateContainer` . Po každém dokončení se na vaše předplatné fakturuje jedna hodina využití. Další informace o fakturaci služby Azure Cosmos DB najdete v tématu [Ceny Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="database-examples"></a>Příklady pro databáze

Ukázka [database_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) Python ukazuje, jak provádět následující úlohy. Další informace o databázích Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](databases-containers-items.md) konceptu.

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient. create_database|
| [Čtení databáze podle ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient. get_database_client|
| [Dotazování databází](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient. query_databases|
| [Výpis databází pro účet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient. list_databases|
| [Odstranění databáze](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient. delete_database|

## <a name="container-examples"></a>Příklady pro kontejnery

Ukázka [container_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) Python ukazuje, jak provádět následující úlohy. Informace o kolekcích Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](databases-containers-items.md) konceptu.

| Úkol | API – referenční informace |
| --- | --- |
| [Dotaz na kontejner](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |databáze. query_containers |
| [Vytvoření kontejneru](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |databáze. create_container |
| [Vypsat všechny kontejnery v databázi](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |databáze. list_containers |
| [Získat kontejner podle jeho ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |databáze. get_container_client |
| [Správa zřízené propustnosti kontejneru](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |Container. read_offer, Container. replace_throughput|
| [Odstranění kontejneru](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |databáze. delete_container |

## <a name="item-examples"></a>Příklady pro položky

Ukázka [item_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) Python ukazuje, jak provádět následující úlohy. Další informace o dokumentech Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](databases-containers-items.md) konceptu.

| Úkol | API – referenční informace |
| --- | --- |
| [Vytváření položek v kontejneru](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |kontejner. create_item |
| [Číst položku podle jejího ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |kontejner. read_item |
| [Načte všechny položky v kontejneru.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |kontejner. read_all_items |
| [Dotazování položky podle jejího ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |kontejner. query_items |
| [Nahrazení položky](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |kontejner. replace_items |
| [Upsert položku](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |kontejner. upsert_item |
| [Odstranění položky](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |kontejner. delete_item |
| [Získání kanálu změn položek v kontejneru](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |kontejner. query_items_change_feed |

## <a name="indexing-examples"></a>Příklady indexování

Ukázka [index_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) Python ukazuje, jak provádět následující úlohy. Další informace o indexování v Azure Cosmos DB před spuštěním následujících ukázek najdete v článku o [zásadách indexování](index-policy.md), [typech indexování](index-types.md)a [indexovaných cestách](index-paths.md) .

| Úkol | API – referenční informace |
| --- | --- |
| [Vyloučí konkrétní položku z indexování.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Použití ručního indexování s indexovanými konkrétními položkami](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | Document. IndexingDirective. include |
| [Vyloučit cesty z indexování](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Definovat cesty, které mají být vyloučeny ve `IndexingPolicy` vlastnosti |
| [Použití indexů rozsahu na řetězce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Definujte zásady indexování s indexy rozsahu pro datový typ String. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Provedení transformace indexu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |Database. replace_container (použití aktualizovaných zásad indexování)|
| [Použít kontroly, pokud v cestě existuje jenom index hash.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | nastavení `enable_scan_in_query=True` a `enable_cross_partition_query=True` při dotazování na položky |
