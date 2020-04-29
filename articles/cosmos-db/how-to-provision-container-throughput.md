---
title: Zřízení propustnosti kontejneru ve službě Azure Cosmos DB
description: Naučte se zřizovat propustnost na úrovni kontejneru v Azure Cosmos DB pomocí Azure Portal, CLI, PowerShellu a různých dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: e416501cb3c532b3ba0a262442b35b236875a463
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273296"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Zajištění propustnosti v kontejneru Azure Cosmos

Tento článek vysvětluje, jak zřídit propustnost pro kontejner (kolekci, graf nebo tabulku) v Azure Cosmos DB. Můžete zřídit propustnost v jednom kontejneru nebo [zřídit propustnost databáze](how-to-provision-database-throughput.md) a sdílet ji mezi kontejnery v rámci databáze. Propustnost můžete zřídit na kontejneru pomocí Azure Portal, rozhraní příkazového řádku Azure nebo sady Azure Cosmos DB SDK.

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet Azure Cosmos.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová kolekce**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte novou databázi, nebo použijte existující.
   * Zadejte ID kontejneru (nebo tabulky nebo grafu).
   * Zadejte hodnotu klíče oddílu (například `/userid`).
   * Zadejte propustnost, kterou chcete zřídit (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky Průzkumník dat se zvýrazněnou novou kolekcí](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Rozhraní příkazového řádku Azure CLI nebo PowerShell

Pokud chcete vytvořit kontejner s vyhrazenou propustností, přečtěte si téma.

* [Vytvoření kontejneru pomocí Azure CLI](manage-with-cli.md#create-a-container)
* [Vytvoření kontejneru pomocí PowerShellu](manage-with-powershell.md#create-container)

> [!Note]
> Pokud zřizujete propustnost na kontejneru v účtu Azure Cosmos, který je nakonfigurovaný s rozhraním API Azure Cosmos DB pro MongoDB, použijte `/myShardKey` pro cestu ke klíči oddílu. Pokud zřizujete propustnost na kontejneru v účtu Azure Cosmos nakonfigurovaném pomocí rozhraní API Cassandra, použijte `/myPrimaryKey` pro cestu ke klíči oddílu.

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Pomocí sad Cosmos SDK pro SQL API můžete zřídit propustnost pro všechna Cosmos DB rozhraní API, s výjimkou rozhraní API Cassandra.

### <a name="sql-mongodb-gremlin-and-table-apis"></a><a id="dotnet-most"></a>Rozhraní SQL API, MongoDB API, Gremlin API a rozhraní API pro tabulky
### <a name="net-v2-sdk"></a>Sada .NET v2 SDK

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>Sada .NET V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Rozhraní Cassandra API

Podobné příkazy lze vydat prostřednictvím libovolného ovladače kompatibilního s CQL.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Změna nebo změna propustnosti pro tabulku Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Další kroky

Informace o zřizování propustnosti v Azure Cosmos DB najdete v následujících článcích:

* [Jak zřídit propustnost pro databázi](how-to-provision-database-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
