---
title: Zřízení propustnosti kontejneru ve službě Azure Cosmos DB
description: Zjistěte, jak ve službě Azure Cosmos DB zřídit propustnost na úrovni kontejneru.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: f195eaa0f5d22160de8c1e9e2f429073de001828
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986026"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Zřizování propustnosti kontejneru Azure Cosmos

Tento článek vysvětluje, jak zřídit propustnosti na kontejner (kolekci, graf nebo tabulku) ve službě Azure Cosmos DB. Propustnost na jeden kontejner, můžete zřizovat nebo [zřídit propustnost v databázi](how-to-provision-database-throughput.md) a sdílet mezi kontejnery v rámci databáze. Můžete zřídit propustnost v kontejneru pomocí webu Azure portal, rozhraní příkazového řádku Azure nebo sady SDK služby Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Zřízení propustnosti pomocí webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account), nebo vyberte existující účet Azure Cosmos.

1. Otevřít **Průzkumník dat** podokně a vyberte **novou kolekci**. Dále zadejte následující podrobnosti:

   * Označuje, zda vytváříte novou databázi nebo použití existující.
   * Zadejte ID kontejneru (nebo tabulka nebo graf).
   * Zadejte hodnotu klíče oddílu (například `/userid`).
   * Zadejte propustnost, které chcete ke zřízení (třeba 1 000 ru).
   * Vyberte **OK**.

![Snímek obrazovky z Průzkumníku dat s novou kolekci zvýrazněnou](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

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

## <a name="provision-throughput-using-powershell"></a>Zřídit propustnost pomocí Powershellu

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

Pokud zřizujete propustnosti kontejneru v účtu služby Azure Cosmos, který je nakonfigurovaný pro MongoDB API služby Azure Cosmos DB, použijte `/myShardKey` pro cestu ke klíči oddílů. Pokud zřizujete propustnosti kontejneru v účtu služby Azure Cosmos, který je nakonfigurovaný s rozhraním Cassandra API, použijte `/myPrimaryKey` pro cestu ke klíči oddílů.

## <a name="provision-throughput-by-using-net-sdk"></a>Zřizování propustnosti s použitím sady .NET SDK

> [!Note]
> Pomocí sady SDK Cosmos pro rozhraní SQL API služby zřizování propustnosti pro všechny API Cosmos DB, s výjimkou rozhraní Cassandra API.

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

Zobrazit další informace o zřizování propustnosti ve službě Azure Cosmos DB v následujících článcích:

* [Jak zřídit propustnost v databázi](how-to-provision-database-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
