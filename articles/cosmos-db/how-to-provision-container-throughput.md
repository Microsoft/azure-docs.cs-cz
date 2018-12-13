---
title: Zřízení propustnosti kontejneru ve službě Azure Cosmos DB
description: Zjistěte, jak ve službě Azure Cosmos DB zřídit propustnost na úrovni kontejneru.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: dd47976bca75569142f1912eee06c66061e92fa6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097661"
---
# <a name="provision-throughput-for-an-azure-cosmos-db-container"></a>Zřízení propustnosti pro kontejner Azure Cosmos DB

Tento článek vysvětluje, jak zřídit propustnost pro kontejner (kolekci, graf, tabulku) ve službě Azure Cosmos DB. Propustnost můžete zřídit pro jeden kontejner nebo [pro databázi](how-to-provision-database-throughput.md) a sdílet ji mezi kontejnery v rámci databáze. Propustnost pro kontejner můžete zřídit pomocí webu Azure Portal, Azure CLI nebo sad SDK služby Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Zřízení propustnosti pomocí webu Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet služby Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová kolekce**. Potom ve formuláři vyplňte následující podrobnosti:

   * Vytvořte novou databázi nebo použijte existující.
   * Zadejte ID kolekce (případně tabulky nebo grafu).
   * Zadejte hodnotu klíče oddílu, například `/userid`.
   * Zadejte propustnost, například 1 000 RU.
   * Vyberte **OK**.

![Zřízení propustnosti kontejneru v rozhraní SQL API](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Zřízení propustnosti pomocí Azure CLI

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

Pokud zřizujete propustnost pro účet rozhraní MongoDB API, použijte jako cestu ke klíči oddílu /myShardKey, a pokud zřizujete propustnost pro účet rozhraní API Cassandra, použijte jako cestu ke klíči oddílu /myPrimaryKey.

## <a name="provision-throughput-using-net-sdk"></a>Zřízení propustnosti pomocí sady .NET SDK

> [!Note]
> Propustnost pro všechna rozhraní API s výjimkou rozhraní API Cassandra můžete zřídit pomocí rozhraní SQL API.

### <a id="dotnet-most"></a>Rozhraní SQL API, MongoDB API, Gremlin API a rozhraní API pro tabulky

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>Rozhraní API Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Další postup

V následujících článcích najdete informace o zřizování propustnosti ve službě Cosmos DB:

* [Zřízení propustnosti pro databázi](how-to-provision-database-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
