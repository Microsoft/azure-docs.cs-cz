---
title: Zřízení propustnosti kontejneru ve službě Azure Cosmos DB
description: Zjistěte, jak ve službě Azure Cosmos DB zřídit propustnost na úrovni kontejneru.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: 0975fe5135bbe9f5e1dc65ee0444cc3aab986a2e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093054"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Zajištění propustnosti v kontejneru Azure Cosmos

Tento článek vysvětluje, jak zřídit propustnost pro kontejner (kolekci, graf nebo tabulku) v Azure Cosmos DB. Můžete zřídit propustnost v jednom kontejneru nebo [zřídit propustnost databáze](how-to-provision-database-throughput.md) a sdílet ji mezi kontejnery v rámci databáze. Propustnost můžete zřídit na kontejneru pomocí Azure Portal, rozhraní příkazového řádku Azure nebo sady Azure Cosmos DB SDK.

## <a name="provision-throughput-using-azure-portal"></a>Zřízení propustnosti pomocí webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet Azure Cosmos.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová kolekce**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte novou databázi, nebo použijte existující.
   * Zadejte ID kontejneru (nebo tabulky nebo grafu).
   * Zadejte hodnotu klíče oddílu (například `/userid`).
   * Zadejte propustnost, kterou chcete zřídit (například 1000 ru).
   * Vyberte **OK**.

![Snímek obrazovky Průzkumník dat se zvýrazněnou novou kolekcí](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Zřízení propustnosti pomocí Azure CLI

```azurecli-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 400
```

## <a name="provision-throughput-using-powershell"></a>Zajištění propustnosti pomocí PowerShellu

```azurepowershell-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

Pokud zřizujete propustnost na kontejneru v účtu Azure Cosmos, který je nakonfigurovaný s rozhraním API Azure Cosmos DB pro MongoDB, použijte `/myShardKey` pro cestu ke klíči oddílu. Pokud zřizujete propustnost na kontejneru v účtu Azure Cosmos nakonfigurovaném pomocí rozhraní API Cassandra, použijte `/myPrimaryKey` pro cestu ke klíči oddílu.

## <a name="provision-throughput-by-using-net-sdk"></a>Zajištění propustnosti pomocí sady .NET SDK

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

### <a id="dotnet-cassandra"></a>Rozhraní API Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Další kroky

Informace o zřizování propustnosti v Azure Cosmos DB najdete v následujících článcích:

* [Jak zřídit propustnost pro databázi](how-to-provision-database-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
