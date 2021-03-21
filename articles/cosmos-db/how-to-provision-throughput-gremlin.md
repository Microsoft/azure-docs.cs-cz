---
title: Zajištění propustnosti pro Azure Cosmos DB prostředky rozhraní API Gremlin
description: Naučte se, jak zajistit propustnost kontejnerů, databází a automatického škálování v Azure Cosmos DB prostředky rozhraní API Gremlin. Budete používat Azure Portal, CLI, PowerShell a různé další sady SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 3c2af7f33135a8c6621db233451231ffa89c2d64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93086155"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Zřizování databáze, kontejneru nebo propustnosti automatického škálování v Azure Cosmos DB prostředky rozhraní API Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Tento článek vysvětluje, jak zřídit propustnost v rozhraní Azure Cosmos DB Gremlin API. Můžete zřídit standardní (ruční) nebo propustnost automatického škálování na kontejneru nebo databázi a sdílet ji mezi kontejnery v rámci databáze. Propustnost můžete zřídit pomocí Azure Portal, rozhraní příkazového řádku Azure nebo sady Azure Cosmos DB SDK.

Pokud používáte jiné rozhraní API, přečtěte si článek rozhraní API pro [SQL](how-to-provision-container-throughput.md), [rozhraní API Cassandra](how-to-provision-throughput-cassandra.md), [rozhraní API pro MongoDB](how-to-provision-throughput-mongodb.md) , které zajistí propustnost.

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)nebo vyberte existující účet Azure Cosmos.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový graf**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte novou databázi, nebo použijte existující. Vyberte možnost **zřízení propustnosti databáze** , pokud chcete zřídit propustnost na úrovni databáze.
   * Zadejte ID grafu.
   * Zadejte hodnotu klíče oddílu (například `/ItemID` ).
   * Zadejte propustnost, kterou chcete zřídit (například 1000 ru).
   * Vyberte **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Snímek obrazovky Průzkumník dat při vytváření nového grafu s propustností na úrovni databáze":::

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Pomocí sad Cosmos SDK pro SQL API můžete zřídit propustnost pro všechna rozhraní API Azure Cosmos DB s výjimkou rozhraní Cassandra a MongoDB API.

### <a name="provision-container-level-throughput"></a>Zřídit propustnost na úrovni kontejneru

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

### <a name="provision-database-level-throughput"></a>Zřídit propustnost na úrovni databáze

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Šablony Azure Resource Manager lze použít ke zřízení propustnosti automatického škálování pro databáze nebo prostředky na úrovni kontejneru pro všechna rozhraní API Azure Cosmos DB. Ukázky najdete v tématu [šablony Azure Resource Manager pro Azure Cosmos DB](templates-samples-gremlin.md) .

## <a name="azure-cli"></a>Azure CLI

Azure CLI se dá využít ke zřízení propustnosti automatického škálování u databáze nebo prostředků na úrovni kontejneru pro všechna Azure Cosmos DB rozhraní API. Ukázky najdete v [ukázkách Azure CLI pro Azure Cosmos DB](cli-samples-gremlin.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell lze použít ke zřízení propustnosti automatického škálování u databáze nebo prostředků na úrovni kontejneru pro všechna rozhraní API Azure Cosmos DB. Ukázky najdete v tématu [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples-gremlin.md).

## <a name="next-steps"></a>Další kroky

Informace o zřizování propustnosti v Azure Cosmos DB najdete v následujících článcích:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)