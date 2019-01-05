---
title: Vytvoření kontejneru ve službě Azure Cosmos DB
description: Zjistěte, jak vytvořit kontejner ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 7434407c7d1afe197a560d9ed9c4f1e7e94b6c2b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033532"
---
# <a name="create-a-container-in-azure-cosmos-db"></a>Vytvoření kontejneru ve službě Azure Cosmos DB

Tento článek popisuje různé způsoby vytvoření kontejneru (kolekce, tabulky, grafu). Kontejner je možné vytvořit pomocí webu Azure Portal, Azure CLI nebo podporovaných sad SDK. Tento článek popisuje, jak vytvořit kontejner, zadat klíč oddílu a zřídit propustnost.

## <a name="create-a-container-using-azure-portal"></a>Vytvoření kontejneru pomocí webu Azure Portal

### <a id="portal-sql"></a>ROZHRANÍ SQL API

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet služby Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová kolekce**. Potom ve formuláři vyplňte následující podrobnosti:

   * Vytvořte novou databázi nebo použijte existující.
   * Zadejte ID kolekce.
   * Zadejte klíč oddílu.
   * Zadejte propustnost, například 1 000 RU.
   * Vyberte **OK**.

![Rozhraní SQL API vytvoří kolekci](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet služby Cosmos DB](create-mongodb-dotnet.md#create-a-database-account) nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová kolekce**. Potom ve formuláři vyplňte následující podrobnosti:

   * Vytvořte novou databázi nebo použijte existující.
   * Zadejte ID kolekce.
   * Vyberte **neomezenou** kapacitu úložiště.
   * Zadejte klíč horizontálního dělení.
   * Zadejte propustnost, například 1 000 RU.
   * Vyberte **OK**.

![Vytvoří kolekci Azure Cosmos DB API pro MongoDB](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Rozhraní API Cassandra

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet služby Cosmos DB](create-cassandra-dotnet.md#create-a-database-account) nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová tabulka**. Potom ve formuláři vyplňte následující podrobnosti:

   * Vytvořte nový prostor klíčů nebo použijte existující.
   * Zadejte název tabulky.
   * Zadejte vlastnosti a určete PRIMÁRNÍ KLÍČ.
   * Zadejte propustnost, například 1 000 RU.
   * Vyberte **OK**.

![Rozhraní API Cassandra vytvoří kolekci](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> V případě rozhraní API Cassandra se jako klíč oddílu používá primární klíč.

### <a id="portal-gremlin"></a>Rozhraní Gremlin API

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet služby Cosmos DB](create-graph-dotnet.md#create-a-database-account) nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový graf**. Potom ve formuláři vyplňte následující podrobnosti:

   * Vytvořte novou databázi nebo použijte existující.
   * Zadejte ID grafu.
   * Vyberte **neomezenou** kapacitu úložiště.
   * Zadejte klíč oddílu pro vrcholy.
   * Zadejte propustnost, například 1 000 RU.
   * Vyberte **OK**.

![Rozhraní Gremlin API vytvoří kolekci](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Rozhraní API pro tabulky

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet služby Cosmos DB](create-table-dotnet.md#create-a-database-account) nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová tabulka**. Potom ve formuláři vyplňte následující podrobnosti:

   * Zadejte ID tabulky.
   * Vyberte **neomezenou** kapacitu úložiště.
   * Zadejte propustnost, například 1 000 RU.
   * Vyberte **OK**.

![Rozhraní API pro tabulky vytvoří kolekci](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> V případě rozhraní API pro tabulky se klíč oddílu zadává při každém přidání nového řádku.

## <a name="create-a-container-using-azure-cli"></a>Vytvoření kontejneru pomocí Azure CLI

### <a id="cli-sql"></a>ROZHRANÍ SQL API

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>Rozhraní API Cassandra

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>Rozhraní Gremlin API

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>Rozhraní API pro tabulky

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-using-net-sdk"></a>Vytvoření kontejneru pomocí sady .NET SDK

### <a id="dotnet-sql-graph"></a>Rozhraní SQL API a Gremlin API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
Přenosový protokol MongoDB nemá koncept jednotky žádostí. Chcete-li vytvořit novou kolekci s propustností, pomocí webu Azure Portal nebo rozhraní SQL API jak ukazují příklady theprevious.

### <a id="dotnet-cassandra"></a>Rozhraní API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Další postup

V následujících článcích najdete informace o dělení ve službě Cosmos DB:

- [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
