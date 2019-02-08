---
title: Vytvoření kontejneru ve službě Azure Cosmos DB
description: Zjistěte, jak vytvořit kontejner ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 445d9f220a215eb17436d52f637b57bd3492aaae
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864770"
---
# <a name="create-an-azure-cosmos-container"></a>Vytvoření kontejneru Azure Cosmos

Tento článek popisuje různé způsoby, jak vytvořit kontejner (kolekci, tabulka nebo graf). Použití na webu Azure portal, rozhraní příkazového řádku Azure nebo podporovaných sad SDK. Tento článek ukazuje, jak vytvořit kontejner, zadejte klíč oddílu a zřídit propustnost.

## <a name="create-a-container-by-using-azure-portal"></a>Vytvořte kontejner s využitím webu Azure portal

### <a id="portal-sql"></a>SQL API

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvoření nového účtu služby Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **novou kolekci**. Dále zadejte následující podrobnosti:

   * Označuje, zda vytváříte novou databázi nebo použití existující.
   * Zadejte ID kolekce.
   * Zadejte klíč oddílu.
   * Zadejte propustnost (třeba 1 000 ru).
   * Vyberte **OK**.

![Průzkumník dat – snímek obrazovky podokna s novou kolekci zvýrazněnou](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>Azure Cosmos DB přes rozhraní API pro MongoDB

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvoření nového účtu služby Azure Cosmos DB](create-mongodb-dotnet.md#create-a-database-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **novou kolekci**. Dále zadejte následující podrobnosti:

   * Označuje, zda vytváříte novou databázi nebo použití existující.
   * Zadejte ID kolekce.
   * Vyberte **neomezenou** kapacitu úložiště.
   * Zadejte klíč horizontálního oddílu.
   * Zadejte propustnost (třeba 1 000 ru).
   * Vyberte **OK**.

![Snímek obrazovky z Azure Cosmos DB API pro MongoDB, dialogové okno Přidat kolekci](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Rozhraní API Cassandra

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvoření nového účtu služby Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **nová tabulka**. Dále zadejte následující podrobnosti:

   * Označuje, zda vytváříte nový prostor klíčů, nebo použití existující.
   * Zadejte název tabulky.
   * Zadejte vlastnosti a určit primární klíč.
   * Zadejte propustnost (třeba 1 000 ru).
   * Vyberte **OK**.

![Snímek obrazovky z rozhraní Cassandra API, dialogové okno Přidat tabulku](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> V případě rozhraní API Cassandra se jako klíč oddílu používá primární klíč.

### <a id="portal-gremlin"></a>Rozhraní Gremlin API

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvoření nového účtu služby Azure Cosmos DB](create-graph-dotnet.md#create-a-database-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **nový graf**. Dále zadejte následující podrobnosti:

   * Označuje, zda vytváříte novou databázi, nebo použití existující.
   * Zadejte ID grafu.
   * Vyberte **neomezenou** kapacitu úložiště.
   * Zadejte klíč oddílu pro vrcholy.
   * Zadejte propustnost (třeba 1 000 ru).
   * Vyberte **OK**.

![Snímek obrazovky z rozhraní Gremlin API, dialogové okno Přidat graf](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Rozhraní API pro tabulky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvoření nového účtu služby Azure Cosmos DB](create-table-dotnet.md#create-a-database-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **nová tabulka**. Dále zadejte následující podrobnosti:

   * Zadejte ID tabulky.
   * Vyberte **neomezenou** kapacitu úložiště.
   * Zadejte propustnost (třeba 1 000 ru).
   * Vyberte **OK**.

![Snímek obrazovky z rozhraní Table API, dialogové okno Přidat tabulku](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> V případě rozhraní API pro tabulky se klíč oddílu zadává při každém přidání nového řádku.

## <a name="create-a-container-by-using-azure-cli"></a>Vytvořte kontejner s použitím rozhraní příkazového řádku Azure

### <a id="cli-sql"></a>SQL API

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

### <a id="cli-mongodb"></a>Azure Cosmos DB přes rozhraní API pro MongoDB

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

## <a name="create-a-container-by-using-net-sdk"></a>Vytvořte kontejner s použitím sady .NET SDK

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

### <a id="dotnet-mongodb"></a>Azure Cosmos DB přes rozhraní API pro MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
Přenosový protokol MongoDB neuvede v požadavku jednotky. Chcete-li vytvořit novou kolekci s propustností, pomocí webu Azure portal nebo rozhraní SQL API.

### <a id="dotnet-cassandra"></a>Rozhraní API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Další postup

- [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
