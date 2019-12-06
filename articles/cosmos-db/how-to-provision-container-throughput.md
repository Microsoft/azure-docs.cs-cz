---
title: Zřízení propustnosti kontejneru ve službě Azure Cosmos DB
description: Naučte se zřizovat propustnost na úrovni kontejneru v Azure Cosmos DB pomocí Azure Portal, CLI, PowerShellu a různých dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: mjbrown
ms.openlocfilehash: ed6a55c8b6049f16e96a4a95ecf3ef125db908d7
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872039"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Zajištění propustnosti v kontejneru Azure Cosmos

Tento článek vysvětluje, jak zřídit propustnost pro kontejner (kolekci, graf nebo tabulku) v Azure Cosmos DB. Můžete zřídit propustnost v jednom kontejneru nebo [zřídit propustnost databáze](how-to-provision-database-throughput.md) a sdílet ji mezi kontejnery v rámci databáze. Propustnost můžete zřídit na kontejneru pomocí Azure Portal, rozhraní příkazového řádku Azure nebo sady Azure Cosmos DB SDK.

## <a name="azure-portal"></a>Portál Azure

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

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
> Pokud zřizujete propustnost na kontejneru v účtu Azure Cosmos nakonfigurovaném s rozhraním API Azure Cosmos DB pro MongoDB, použijte pro cestu ke klíči oddílu `/myShardKey`. Pokud zřizujete propustnost na kontejneru v účtu Azure Cosmos nakonfigurovaném pomocí rozhraní API Cassandra, použijte pro cestu ke klíči oddílu `/myPrimaryKey`.

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Pomocí sad Cosmos SDK pro SQL API můžete zřídit propustnost pro všechna Cosmos DB rozhraní API, s výjimkou rozhraní API Cassandra.

### <a id="dotnet-most"></a>Rozhraní SQL API, MongoDB API, Gremlin API a rozhraní API pro tabulky
### <a name="net-v2-sdk"></a>.Net V2 SDK

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

### <a name="net-v3-sdk"></a>.Net V3 SDK
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "contaierId ",
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

### <a id="dotnet-cassandra"></a>Rozhraní API Cassandra

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
