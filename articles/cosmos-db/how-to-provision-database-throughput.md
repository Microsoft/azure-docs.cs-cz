---
title: Zřízení propustnosti databáze ve službě Azure Cosmos DB
description: Naučte se zřizovat propustnost na úrovni databáze v Azure Cosmos DB pomocí Azure Portal, CLI, PowerShellu a různých dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 0ba86d6955805fc97743a1a51aa586b179d82235
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82869866"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Zřízení propustnosti pro databázi v Azure Cosmos DB

Tento článek vysvětluje, jak zřídit propustnost pro databázi v Azure Cosmos DB. Můžete zřídit propustnost pro jeden [kontejner](how-to-provision-container-throughput.md)nebo pro databázi a sdílet propustnost mezi kontejnery. Informace o použití úrovně kontejneru a propustnosti na úrovni databáze najdete v článku [případy použití pro zajištění propustnosti v kontejnerech a databázích](set-throughput.md) . Propustnost na úrovni databáze můžete zřídit pomocí Azure Portal nebo Azure Cosmos DB SDK.

## <a name="provision-throughput-using-azure-portal"></a>Zřízení propustnosti pomocí webu Azure Portal

### <a name="sql-core-api"></a><a id="portal-sql"></a>Rozhraní SQL (Core) API

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet Azure Cosmos.

1. Otevřete podokno **Průzkumník dat** a vyberte možnost **Nová databáze**. Zadejte následující podrobnosti:

   * Zadejte ID databáze.
   * Vyberte možnost **zřízení propustnosti**.
   * Zadejte propustnost (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky dialogového okna Nová databáze](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

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
* [Zřízení propustnosti pro kontejner](how-to-provision-container-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
