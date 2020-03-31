---
title: Vytvoření kontejneru ve službě Azure Cosmos DB
description: Zjistěte, jak vytvořit kontejner v Azure Cosmos DB pomocí Portálu Azure, .Net, Java, Pythonu, Node.js a dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 4eaa2974817bfcd8bef83e5139d75a2d4c2ec107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873705"
---
# <a name="create-an-azure-cosmos-container"></a>Vytvoření kontejneru Azure Cosmos

Tento článek vysvětluje různé způsoby vytvoření kontejneru Azure Cosmos (kolekce, tabulka nebo graf). K tomu můžete použít portál Azure, azure cli nebo podporované sady SDK. Tento článek ukazuje, jak vytvořit kontejner, zadejte klíč oddílu a zřídit propustnost.

## <a name="create-a-container-using-azure-portal"></a>Vytvoření kontejneru pomocí webu Azure Portal

### <a name="sql-api"></a><a id="portal-sql"></a>SQL API

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový kontejner**. Dále uveďte následující podrobnosti:

   * Určete, zda vytváříte novou databázi nebo používáte existující databázi.
   * Zadejte ID kontejneru.
   * Zadejte klíč oddílu.
   * Zadejte propustnost, která má být zřízena (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky podokna Průzkumník dat se zvýrazněnou možností Nový kontejner](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový kontejner**. Dále uveďte následující podrobnosti:

   * Určete, zda vytváříte novou databázi nebo používáte existující databázi.
   * Zadejte ID kontejneru.
   * Zadejte klíč štřepů.
   * Zadejte propustnost, která má být zřízena (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky s rozhraním API Azure Cosmos DB pro mongoDB, dialogové okno Přidat kontejner](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Rozhraní API Cassandra

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová tabulka**. Dále uveďte následující podrobnosti:

   * Určete, zda vytváříte nový prostor klíčů nebo používáte existující.
   * Zadejte název tabulky.
   * Zadejte vlastnosti a zadejte primární klíč.
   * Zadejte propustnost, která má být zřízena (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky s rozhraním Cassandra API, dialogové okno Přidat tabulku](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> V případě rozhraní API Cassandra se jako klíč oddílu používá primární klíč.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Rozhraní Gremlin API

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-graph-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový graf**. Dále uveďte následující podrobnosti:

   * Určete, zda vytváříte novou databázi nebo používáte existující databázi.
   * Zadejte ID grafu.
   * Vyberte **neomezenou** kapacitu úložiště.
   * Zadejte klíč oddílu pro vrcholy.
   * Zadejte propustnost, která má být zřízena (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky s rozhraním Gremlin API, dialogové okno Přidat graf](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a name="table-api"></a><a id="portal-table"></a>Rozhraní Table API

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-table-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová tabulka**. Dále uveďte následující podrobnosti:

   * Zadejte ID tabulky.
   * Zadejte propustnost, která má být zřízena (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky s dialogovým oknem Přidat tabulku](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> V případě rozhraní API pro tabulky se klíč oddílu zadává při každém přidání nového řádku.

## <a name="create-a-container-using-azure-cli"></a>Vytvoření kontejneru pomocí azure cli<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Následující odkazy ukazují, jak vytvořit prostředky kontejneru pro Azure Cosmos DB pomocí Azure CLI.

Výpis všech ukázek Rozhraní příkazového systému Azure ve všech rozhraních API db Azure Cosmos najdete v [článku ROZHRANÍ API SQL](cli-samples.md), rozhraní API [Cassandra](cli-samples-cassandra.md), rozhraní [API MongoDB](cli-samples-mongodb.md), [rozhraní API Gremlin](cli-samples-gremlin.md)a [rozhraní API tabulky.](cli-samples-table.md)

* [Vytvoření kontejneru pomocí azure cli](manage-with-cli.md#create-a-container)
* [Vytvoření kolekce pro Azure Cosmos DB pro rozhraní API MongoDB pomocí rozhraní API Azure](./scripts/cli/mongodb/create.md)
* [Vytvoření tabulky Cassandra pomocí rozhraní příkazového příkazu Azure](./scripts/cli/cassandra/create.md)
* [Vytvoření gremlinského grafu pomocí azure cli](./scripts/cli/gremlin/create.md)
* [Vytvoření tabulky rozhraní API s rozhraním API Azure](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershella-idps-mongodba-idps-gremlin"></a>Vytvoření kontejneru pomocí PowerShellu<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

Následující odkazy ukazují, jak vytvořit prostředky kontejneru pro Azure Cosmos DB pomocí PowerShellu.

Výpis všech ukázek Rozhraní příkazového systému Azure ve všech rozhraních API db Azure Cosmos najdete v [článku ROZHRANÍ API SQL](powershell-samples-sql.md), rozhraní API [Cassandra](powershell-samples-cassandra.md), rozhraní [API MongoDB](powershell-samples-mongodb.md), [rozhraní API Gremlin](powershell-samples-gremlin.md)a [rozhraní API tabulky.](powershell-samples-table.md)

* [Vytvoření kontejneru s Powershellem](manage-with-powershell.md#create-container)
* [Vytvoření kolekce pro Azure Cosmos DB pro rozhraní MongoDB API s Powershellem](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [Vytvoření tabulky Cassandra s powershellem](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [Vytvoření gremlinského grafu pomocí powershellu](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [Vytvoření tabulky rozhraní TABLE API s Powershellem](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>Vytvoření kontejneru pomocí sady .NET SDK

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>Rozhraní SQL API a Gremlin API

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

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Drátový protokol MongoDB nerozumí pojmu [jednotky požadavků](request-units.md). Chcete-li vytvořit novou kolekci se zřízenou propustností, použijte portál Azure nebo sady SDK Cosmos DB pro rozhraní SQL API.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Rozhraní API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky požadavků v Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](account-overview.md)
