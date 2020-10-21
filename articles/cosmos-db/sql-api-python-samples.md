---
title: Příklady SQL API Pythonu pro Azure Cosmos DB
description: Příklady v Pythonu najdete na GitHubu pro běžné úlohy v Azure Cosmos DB, včetně operací CRUD.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 08/11/2020
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: ba4382e03e6fc1acfd606a375a062c05581f712c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278219"
---
# <a name="azure-cosmos-db-python-examples"></a>Příklady Azure Cosmos DB Python

> [!div class="op_single_selector"]
> * [Příklady sady .NET v2 SDK](sql-api-dotnet-samples.md)
> * [Příklady sady .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Příklady sady Java v4 SDK](sql-api-java-sdk-samples.md)
> * [Příklady sad data pružiny V3 SDK](sql-api-spring-data-sdk-samples.md)
> * [Příklady v Node.js](sql-api-nodejs-samples.md)
> * [Příklady v Pythonu](sql-api-python-samples.md)
> * [Galerie ukázkového kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Ukázková řešení, která dělají operace CRUD a další běžné operace týkající se Azure Cosmos DB prostředků, jsou součástí úložiště GitHub [Azure-DocumentDB-Python](https://github.com/Azure/azure-documentdb-python) . Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v Pythonu.
* Odkazy na související referenční obsah rozhraní API.

## <a name="prerequisites"></a>Předpoklady

- Účet Cosmos DB. Máte tyto možnosti:
    * V rámci předplatného Azure Active:
        * [Vytvořte si bezplatný účet Azure](https://azure.microsoft.com/free) nebo použijte stávající předplatné. 
        * [Měsíční kredity sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB úroveň Free](https://docs.microsoft.com/azure/cosmos-db/optimize-dev-test#azure-cosmos-db-free-tier)
    * Bez aktivního předplatného Azure:
        * [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/), což je testovací prostředí, které trvá po dobu 30 dnů.
        * [Emulátor služby Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) 
- [Python 2,7 nebo 3.5.3 +](https://www.python.org/downloads/), se `python` spustitelným souborem v `PATH` .
- [Visual Studio Code](https://code.visualstudio.com/).
- [Přípona Pythonu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 
- [Azure Cosmos DB SQL API SDK pro Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="database-examples"></a>Příklady pro databáze

Ukázka [database_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) Python ukazuje, jak provádět následující úlohy. Další informace o databázích Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md) konceptu.

| Úloha | API – referenční informace |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [Čtení databáze podle ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [Dotazování databází](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [Výpis databází pro účet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [Odstranění databáze](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>Příklady pro kontejnery

Ukázka [container_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) Python ukazuje, jak provádět následující úlohy. Informace o kolekcích Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md) konceptu.

| Úloha | API – referenční informace |
| --- | --- |
| [Dotaz na kontejner](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [Vytvoření kontejneru](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [Vypsat všechny kontejnery v databázi](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [Získat kontejner podle jeho ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [Správa zřízené propustnosti kontejneru](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer container.replace_throughput|
| [Odstranění kontejneru](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>Příklady pro položky

Ukázka [item_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) Python ukazuje, jak provádět následující úlohy. Další informace o dokumentech Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md) konceptu.

| Úloha | API – referenční informace |
| --- | --- |
| [Vytváření položek v kontejneru](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [Číst položku podle jejího ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [Načte všechny položky v kontejneru.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [Dotazování položky podle jejího ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [Nahrazení položky](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [Upsert položku](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [Odstranění položky](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [Získání kanálu změn položek v kontejneru](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>Příklady indexování

Ukázka [index_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) Python ukazuje, jak provádět následující úlohy. Další informace o indexování v Azure Cosmos DB před spuštěním následujících ukázek najdete v článku o [zásadách indexování](index-policy.md), [typech indexování](index-types.md)a [indexovaných cestách](index-paths.md) .

| Úloha | API – referenční informace |
| --- | --- |
| [Vyloučí konkrétní položku z indexování.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Použití ručního indexování s indexovanými konkrétními položkami](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | Document. IndexingDirective. include |
| [Vyloučit cesty z indexování](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Definovat cesty, které mají být vyloučeny ve `IndexingPolicy` vlastnosti |
| [Použití indexů rozsahu na řetězce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Definujte zásady indexování s indexy rozsahu pro datový typ String. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Provedení transformace indexu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container (použití aktualizovaných zásad indexování)|
| [Použít kontroly, pokud v cestě existuje jenom index hash.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | nastavení `enable_scan_in_query=True` a `enable_cross_partition_query=True` při dotazování na položky |
