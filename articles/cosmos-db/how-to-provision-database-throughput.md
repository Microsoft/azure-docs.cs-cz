---
title: Zřízení propustnosti databáze ve službě Azure Cosmos DB
description: Zjistěte, jak zřídit propustnost na úrovni databáze ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: 2744422e2e082c5bc6f63975b1100f336d32d5fa
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250062"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Zřizování propustnosti na databázi ve službě Azure Cosmos DB

Tento článek vysvětluje, jak zřídit propustnost v databázi ve službě Azure Cosmos DB. Propustnost můžete zřídit pro jeden [kontejner](how-to-provision-container-throughput.md) nebo pro databázi a sdílet ji mezi kontejnery v rámci databáze. Informace o tom, kdy použít propustnosti kontejneru úrovni a úrovni databáze, najdete v článku [případy použití pro zřizování propustnosti na kontejnerech a databází](set-throughput.md) článku. Databáze úrovně propustnosti můžete zřídit pomocí webu Azure portal nebo sady SDK služby Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Zřízení propustnosti pomocí webu Azure Portal

### <a id="portal-sql"></a>Rozhraní SQL (Core) API

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account), nebo vyberte existující účet Azure Cosmos.

1. Otevřít **Průzkumník dat** podokně a vyberte **novou databázi**. Zadejte následující podrobnosti:

   * Zadejte ID databáze. 
   * Vyberte **zřídit propustnost**.
   * Zadejte propustnost (třeba 1 000 ru).
   * Vyberte **OK**.

![Dialogové okno snímek obrazovky s novou databázi](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)


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




## <a name="provision-throughput-using-powershell"></a>Zřídit propustnost pomocí Powershellu

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
> Pro všechna rozhraní API můžete zřídit propustnost pomocí sady SDK Cosmos pro rozhraní SQL API. Volitelně můžete v následujícím příkladu pro Apache Cassandra API také.

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

## <a name="next-steps"></a>Další kroky

Naleznete v následujících článcích se dozvíte o zřízenou propustnost v databázi Azure Cosmos DB:

* [Globálně škálujte zřízená propustnost](scaling-throughput.md)
* [Zřizování propustnosti na kontejnerech a databází](set-throughput.md)
* [Zřízení propustnosti pro kontejner](how-to-provision-container-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
