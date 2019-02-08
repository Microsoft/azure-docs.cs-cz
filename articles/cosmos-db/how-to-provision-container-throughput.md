---
title: Zřízení propustnosti kontejneru ve službě Azure Cosmos DB
description: Zjistěte, jak ve službě Azure Cosmos DB zřídit propustnost na úrovni kontejneru.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 4df8a12581b5d71a76964ca1e3d40c6c53185f67
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860316"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Zřizování propustnosti kontejneru Azure Cosmos

Tento článek vysvětluje, jak zřídit propustnost pro kontejner (kolekci, graf nebo tabulku) ve službě Azure Cosmos DB. Můžete zřídit propustnost pro jedním kontejnerem nebo [zřizování pro databázi](how-to-provision-database-throughput.md) a sdílet mezi kontejnery v rámci databáze. Propustnost kontejneru můžete zřídit pomocí webu Azure portal, rozhraní příkazového řádku Azure nebo sady SDK služby Azure Cosmos DB.

## <a name="provision-throughput-by-using-azure-portal"></a>Propustnost zřízení pomocí webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvoření nového účtu služby Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **novou kolekci**. Dále zadejte následující podrobnosti:

   * Označuje, zda vytváříte novou databázi nebo použití existující.
   * Zadejte ID kolekce (nebo tabulka nebo graf).
   * Zadejte hodnotu klíče oddílu (například `/userid`).
   * Zadejte propustnost (třeba 1 000 ru).
   * Vyberte **OK**.

![Snímek obrazovky z Průzkumníku dat s novou kolekci zvýrazněnou](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-azure-cli"></a>Zřizování propustnosti s použitím rozhraní příkazového řádku Azure

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

Pokud zřizujete propustnosti pro účet služby Azure Cosmos DB nakonfigurovaný pro MongoDB API služby Azure Cosmos DB, použijte `/myShardKey` pro cestu ke klíči oddílů. Pokud zřizujete propustnosti pro účet služby Azure Cosmos DB nakonfigurovaný pro rozhraní Cassandra API, použijte `/myPrimaryKey` pro cestu ke klíči oddílů.

## <a name="provision-throughput-by-using-net-sdk"></a>Zřizování propustnosti s použitím sady .NET SDK

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

Zobrazit další informace o zřizování propustnosti ve službě Azure Cosmos DB v následujících článcích:

* [Zřízení propustnosti pro databázi](how-to-provision-database-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
