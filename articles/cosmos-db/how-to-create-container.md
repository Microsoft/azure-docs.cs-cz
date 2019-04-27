---
title: Vytvoření kontejneru ve službě Azure Cosmos DB
description: Zjistěte, jak vytvořit kontejner ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/17/2019
ms.author: rimman
ms.openlocfilehash: c075a801a877309709258dd6466e68e46d802eff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61056575"
---
# <a name="create-an-azure-cosmos-container"></a>Vytvoření kontejneru Azure Cosmos

Tento článek popisuje různé způsoby, jak vytvořit kontejner služby Azure Cosmos (kolekce, tabulka nebo graf). Pomocí webu Azure portal, rozhraní příkazového řádku Azure, nebo podporovaných sad SDK pro tento. Tento článek ukazuje, jak vytvořit kontejner, zadejte klíč oddílu a zřídit propustnost.

## <a name="create-a-container-using-azure-portal"></a>Vytvoření kontejneru pomocí webu Azure Portal

### <a id="portal-sql"></a>SQL API

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **novou kolekci**. Dále zadejte následující podrobnosti:

   * Označuje, zda vytváříte novou databázi nebo použití existující.
   * Zadejte ID kolekce.
   * Zadejte klíč oddílu.
   * Zadejte propustnost, které se mají zřídit (třeba 1 000 ru).
   * Vyberte **OK**.

![Průzkumník dat – snímek obrazovky podokna s novou kolekci zvýrazněnou](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>Azure Cosmos DB přes rozhraní API pro MongoDB

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **novou kolekci**. Dále zadejte následující podrobnosti:

   * Označuje, zda vytváříte novou databázi nebo použití existující.
   * Zadejte ID kolekce.
   * Zadejte klíč horizontálního oddílu.
   * Zadejte propustnost, které se mají zřídit (třeba 1 000 ru).
   * Vyberte **OK**.

![Snímek obrazovky z Azure Cosmos DB API pro MongoDB, dialogové okno Přidat kolekci](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Rozhraní API Cassandra

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **nová tabulka**. Dále zadejte následující podrobnosti:

   * Označuje, zda vytváříte nový prostor klíčů, nebo použití existující.
   * Zadejte název tabulky.
   * Zadejte vlastnosti a určit primární klíč.
   * Zadejte propustnost, které se mají zřídit (třeba 1 000 ru).
   * Vyberte **OK**.

![Snímek obrazovky z rozhraní Cassandra API, dialogové okno Přidat tabulku](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> V případě rozhraní API Cassandra se jako klíč oddílu používá primární klíč.

### <a id="portal-gremlin"></a>Rozhraní Gremlin API

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-graph-dotnet.md#create-a-database-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **nový graf**. Dále zadejte následující podrobnosti:

   * Označuje, zda vytváříte novou databázi, nebo použití existující.
   * Zadejte ID grafu.
   * Vyberte **neomezenou** kapacitu úložiště.
   * Zadejte klíč oddílu pro vrcholy.
   * Zadejte propustnost, které se mají zřídit (třeba 1 000 ru).
   * Vyberte **OK**.

![Snímek obrazovky z rozhraní Gremlin API, dialogové okno Přidat graf](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Rozhraní API pro tabulky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-table-dotnet.md#create-a-database-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **nová tabulka**. Dále zadejte následující podrobnosti:

   * Zadejte ID tabulky.
   * Zadejte propustnost, které se mají zřídit (třeba 1 000 ru).
   * Vyberte **OK**.

![Snímek obrazovky z rozhraní Table API, dialogové okno Přidat tabulku](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> V případě rozhraní API pro tabulky se klíč oddílu zadává při každém přidání nového řádku.

## <a name="create-a-container-using-azure-cli"></a>Vytvoření kontejneru pomocí Azure CLI

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

### <a id="dotnet-mongodb"></a>Azure Cosmos DB přes rozhraní API pro MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Přenosový protokol MongoDB nerozumí konceptu [jednotek žádostí](request-units.md). Nová kolekce se zřízenou propustností na něm vytvoříte pomocí webu Azure portal nebo Cosmos DB SDK pro rozhraní SQL API.

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
- [Jednotky žádosti v Azure Cosmos DB](request-units.md)
- [Zřizování propustnosti na kontejnerech a databází](set-throughput.md)
- [Práce s účtem Azure Cosmos](account-overview.md)
