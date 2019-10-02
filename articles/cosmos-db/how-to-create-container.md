---
title: Vytvoření kontejneru ve službě Azure Cosmos DB
description: Zjistěte, jak vytvořit kontejner ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 9805ff9aa4932c262db13c47fd2e442b3d3d676f
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811727"
---
# <a name="create-an-azure-cosmos-container"></a>Vytvoření kontejneru Azure Cosmos

Tento článek popisuje různé způsoby vytvoření kontejneru Azure Cosmos (kolekce, tabulka nebo graf). K tomu můžete použít Azure Portal, Azure CLI nebo podporované sady SDK. Tento článek ukazuje, jak vytvořit kontejner, zadat klíč oddílu a zajistit propustnost.

## <a name="create-a-container-using-azure-portal"></a>Vytvoření kontejneru pomocí webu Azure Portal

### <a id="portal-sql"></a>ROZHRANÍ SQL API

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový kontejner**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte novou databázi, nebo použijte existující.
   * Zadejte ID kontejneru.
   * Zadejte klíč oddílu.
   * Zadejte propustnost, která se má zřídit (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky s podoknem Průzkumník dat s zvýrazněným novým kontejnerem](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>Rozhraní API pro MongoDB Azure Cosmos DB

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový kontejner**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte novou databázi, nebo použijte existující.
   * Zadejte ID kontejneru.
   * Zadejte horizontálních oddílů klíč.
   * Zadejte propustnost, která se má zřídit (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky Azure Cosmos DB API pro MongoDB, dialogové okno Přidat kontejner](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Rozhraní API Cassandra

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte možnost **Nová tabulka**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte nové místo na disku, nebo použijte existující.
   * Zadejte název tabulky.
   * Zadejte vlastnosti a zadejte primární klíč.
   * Zadejte propustnost, která se má zřídit (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky rozhraní API Cassandra, dialogové okno Přidat tabulku](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> V případě rozhraní API Cassandra se jako klíč oddílu používá primární klíč.

### <a id="portal-gremlin"></a>Rozhraní Gremlin API

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-graph-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový graf**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte novou databázi, nebo použijte existující.
   * Zadejte ID grafu.
   * Vyberte **neomezenou** kapacitu úložiště.
   * Zadejte klíč oddílu pro vrcholy.
   * Zadejte propustnost, která se má zřídit (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky s rozhraním API Gremlin, dialogové okno Přidat graf](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Rozhraní API pro tabulky

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-table-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte možnost **Nová tabulka**. Dále zadejte následující podrobnosti:

   * Zadejte ID tabulky.
   * Zadejte propustnost, která se má zřídit (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky rozhraní API pro tabulky, dialogové okno Přidat tabulku](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> V případě rozhraní API pro tabulky se klíč oddílu zadává při každém přidání nového řádku.

## Vytvoření kontejneru pomocí Azure CLI<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Níže uvedené odkazy ukazují, jak vytvořit prostředky kontejneru pro Azure Cosmos DB pomocí rozhraní příkazového řádku Azure CLI.

Seznam všech ukázek Azure CLI napříč všemi Azure Cosmos DB rozhraní API najdete v tématu rozhraní API [SQL](cli-samples.md), [rozhraní API Cassandra](cli-samples-cassandra.md), [rozhraní API pro MongoDB](cli-samples-mongodb.md), rozhraní [Gremlin API](cli-samples-gremlin.md)a [rozhraní API pro tabulky](cli-samples-table.md)

* [Vytvoření kontejneru pomocí Azure CLI](manage-with-cli.md#create-a-container)
* [Vytvoření kolekce pro Azure Cosmos DB rozhraní API MongoDB pomocí Azure CLI](/scripts/cli/mongodb/create.md)
* [Vytvoření tabulky Cassandra pomocí Azure CLI](/scripts/cli/cassandra/create.md)
* [Vytvoření grafu Gremlin pomocí Azure CLI](/scripts/cli/gremlin/create.md)
* [Vytvoření tabulky rozhraní API pro tabulky pomocí Azure CLI](/scripts/cli/table/create.md)

## Vytvoření kontejneru pomocí PowerShellu<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

Níže uvedené odkazy ukazují, jak vytvořit prostředky kontejneru pro Azure Cosmos DB pomocí prostředí PowerShell.

Seznam všech ukázek Azure CLI napříč všemi Azure Cosmos DB rozhraní API najdete v tématu rozhraní API [SQL](powershell-samples-sql.md), [rozhraní API Cassandra](powershell-samples-cassandra.md), [rozhraní API pro MongoDB](powershell-samples-mongodb.md), rozhraní [Gremlin API](powershell-samples-gremlin.md)a [rozhraní API pro tabulky](powershell-samples-table.md)

* [Vytvoření kontejneru pomocí PowerShellu](manage-with-powershell.md#create-container)
* [Vytvoření kolekce pro Azure Cosmos DB rozhraní API MongoDB pomocí PowerShellu](/scripts/powershell/mongodb/ps-mongodb-create.md)
* [Vytvoření tabulky Cassandra pomocí prostředí PowerShell](/scripts/powershell/cassandra/ps-cassandra-create.md)
* [Vytvoření grafu Gremlin pomocí prostředí PowerShell](/scripts/powershell/gremlin/ps-gremlin-create.md)
* [Vytvoření tabulky rozhraní API pro tabulky pomocí prostředí PowerShell](/scripts/powershell/table/ps-table-create.md)

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

### <a id="dotnet-mongodb"></a>Rozhraní API pro MongoDB Azure Cosmos DB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB přenosový protokol nerozumí konceptu [jednotek žádostí](request-units.md). Pro vytvoření nové kolekce s zřízenou propustností použijte sady SDK pro rozhraní SQL Azure Portal nebo Cosmos DB.

### <a id="dotnet-cassandra"></a>Rozhraní API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](account-overview.md)
