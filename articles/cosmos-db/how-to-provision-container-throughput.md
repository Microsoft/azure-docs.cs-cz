---
title: Zřízení propustnosti kontejneru v Azure Cosmos DB SQL API
description: Naučte se zřídit propustnost na úrovni kontejneru v Azure Cosmos DB SQL API pomocí Azure Portal, CLI, PowerShellu a různých dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a6855a1c730c33a835e5033041ee7978be28fc6b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278687"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Zřízení standardní (ruční) propustnosti v kontejneru Azure Cosmos – SQL API

Tento článek vysvětluje, jak zřídit standardní (ruční) propustnost na kontejneru v Azure Cosmos DB SQL API. Můžete zřídit propustnost v jednom kontejneru nebo [zřídit propustnost databáze](how-to-provision-database-throughput.md) a sdílet ji mezi kontejnery v rámci databáze. Propustnost můžete zřídit na kontejneru pomocí Azure Portal, rozhraní příkazového řádku Azure nebo sady Azure Cosmos DB SDK.

Pokud používáte jiné rozhraní API, přečtěte si téma [rozhraní API pro MongoDB](how-to-provision-throughput-mongodb.md), [rozhraní API Cassandra](how-to-provision-throughput-cassandra.md), články [Gremlin API](how-to-provision-throughput-gremlin.md) pro zajištění propustnosti.

## <a name="azure-portal"></a>Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet Azure Cosmos.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový kontejner**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte novou databázi, nebo použijte existující.
   * Zadejte ID kontejneru.
   * Zadejte hodnotu klíče oddílu (například `/ItemID` ).
   * Zadejte propustnost, kterou chcete zřídit (například 1000 ru).
   * Vyberte **OK**.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Snímek obrazovky Průzkumník dat se zvýrazněnou novou kolekcí":::

## <a name="azure-cli-or-powershell"></a>Rozhraní příkazového řádku Azure CLI nebo PowerShell

Pokud chcete vytvořit kontejner s vyhrazenou propustností, přečtěte si téma.

* [Vytvoření kontejneru pomocí Azure CLI](manage-with-cli.md#create-a-container)
* [Vytvoření kontejneru pomocí PowerShellu](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Pomocí sad Cosmos SDK pro SQL API můžete zřídit propustnost pro všechna rozhraní API Cosmos DB s výjimkou rozhraní Cassandra a MongoDB API.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

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

## <a name="next-steps"></a>Další kroky

Informace o zřizování propustnosti v Azure Cosmos DB najdete v následujících článcích:

* [Jak zřídit standardní (ruční) propustnost v databázi](how-to-provision-database-throughput.md)
* [Jak zřídit propustnost automatického škálování v databázi](how-to-provision-autoscale-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
