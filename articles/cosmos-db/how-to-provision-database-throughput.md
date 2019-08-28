---
title: Zřízení propustnosti databáze ve službě Azure Cosmos DB
description: Zjistěte, jak zřídit propustnost na úrovni databáze ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: 29bc65c8afaa1fe4bdc39923bd2219184e8b3a96
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093002"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Zřízení propustnosti pro databázi v Azure Cosmos DB

Tento článek vysvětluje, jak zřídit propustnost pro databázi v Azure Cosmos DB. Propustnost můžete zřídit pro jeden [kontejner](how-to-provision-container-throughput.md) nebo pro databázi a sdílet ji mezi kontejnery v rámci databáze. Informace o použití propustnosti na úrovni kontejneru a databáze najdete v článku [případy použití pro zajištění propustnosti v kontejnerech a databázích](set-throughput.md) . Propustnost na úrovni databáze můžete zřídit pomocí Azure Portal nebo Azure Cosmos DB SDK.

## <a name="provision-throughput-using-azure-portal"></a>Zřízení propustnosti pomocí webu Azure Portal

### <a id="portal-sql"></a>Rozhraní SQL (Core) API

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet Azure Cosmos.

1. Otevřete podokno **Průzkumník dat** a vyberte možnost **Nová databáze**. Zadejte následující podrobnosti:

   * Zadejte ID databáze. 
   * Vyberte možnost **zřízení propustnosti**.
   * Zadejte propustnost (například 1000 ru).
   * Vyberte **OK**.

![Snímek obrazovky dialogového okna Nová databáze](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)


## <a name="provision-throughput-using-azure-cli"></a>Zřízení propustnosti pomocí Azure CLI

```azcli-interactive
az cosmosdb database create --db-name
                            [--key]
                            [--name]
                            [--resource-group-name]
                            [--subscription]
                            [--throughput]
                            [--url-connection]
```




## <a name="provision-throughput-using-powershell"></a>Zajištění propustnosti pomocí PowerShellu

```azurepowershell-interactive
# Create a database and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$databaseResourceName = $accountName + "/sql/" + $databaseName

$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties
```

## <a name="provision-throughput-using-net-sdk"></a>Zřízení propustnosti pomocí sady .NET SDK

> [!Note]
> Sady Cosmos SDK pro SQL API můžete použít ke zřízení propustnosti pro všechna rozhraní API. Volitelně můžete pro rozhraní API Cassandra také použít následující příklad.

### <a id="dotnet-all"></a>Všechna rozhraní API
### <a name="net-v2-sdk"></a>.Net V2 SDK

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

### <a name="net-v3-sdk"></a>.Net V3 SDK
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>Rozhraní API Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Další postup

V následujících článcích se dozvíte o zřízené propustnosti v Azure Cosmos DB:

* [Globálně škálovat zřízenou propustnost](scaling-throughput.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Zřízení propustnosti pro kontejner](how-to-provision-container-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
