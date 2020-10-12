---
title: Zřízení propustnosti databáze ve službě Azure Cosmos DB
description: Naučte se zřizovat propustnost na úrovni databáze v Azure Cosmos DB pomocí Azure Portal, CLI, PowerShellu a různých dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 668aa51bdb57dc4bcde0e3a95c481bb60e3d8ed3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88997366"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db"></a>Zřizování standardní (ruční) propustnosti databáze v Azure Cosmos DB

Tento článek vysvětluje, jak zřídit standardní (ruční) propustnost v databázi v Azure Cosmos DB. Můžete zřídit propustnost pro jeden [kontejner](how-to-provision-container-throughput.md)nebo pro databázi a sdílet propustnost mezi kontejnery. Informace o použití úrovně kontejneru a propustnosti na úrovni databáze najdete v článku [případy použití pro zajištění propustnosti v kontejnerech a databázích](set-throughput.md) . Propustnost na úrovni databáze můžete zřídit pomocí Azure Portal nebo Azure Cosmos DB SDK.

## <a name="provision-throughput-using-azure-portal"></a>Zřízení propustnosti pomocí webu Azure Portal

### <a name="sql-core-api"></a><a id="portal-sql"></a>Rozhraní SQL (Core) API

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet Azure Cosmos.

1. Otevřete podokno **Průzkumník dat** a vyberte možnost **Nová databáze**. Zadejte následující podrobnosti:

   * Zadejte ID databáze.
   * Vyberte možnost **zřízení propustnosti**.
   * Zadejte propustnost (například 1000 ru).
   * Vyberte **OK**.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png" alt-text="Snímek obrazovky dialogového okna Nová databáze":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Zajištění propustnosti pomocí Azure CLI nebo PowerShellu

Postup vytvoření databáze se sdílenou propustností najdete v tématu.

* [Vytvoření databáze pomocí Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Vytvoření databáze pomocí prostředí PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Zřízení propustnosti pomocí sady .NET SDK

> [!Note]
> Sady Cosmos SDK pro SQL API můžete použít ke zřízení propustnosti pro všechna rozhraní API. Volitelně můžete pro rozhraní API Cassandra také použít následující příklad.

### <a name="all-apis"></a><a id="dotnet-all"></a>Všechna rozhraní API

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

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Rozhraní Cassandra API

Podobný příkaz lze provést prostřednictvím libovolného ovladače kompatibilního s CQL.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Další kroky

V následujících článcích se dozvíte o zřízené propustnosti v Azure Cosmos DB:

* [Globální škálování zřízené propustnosti](scaling-throughput.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Jak zřídit standardní (ruční) propustnost pro kontejner](how-to-provision-container-throughput.md)
* [Jak zřídit propustnost automatického škálování pro kontejner](how-to-provision-autoscale-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
