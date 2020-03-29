---
title: Zřízení propustnosti databáze ve službě Azure Cosmos DB
description: Zjistěte, jak zřídit propustnost na úrovni databáze v Azure Cosmos DB pomocí portálu Azure, CLI, PowerShellu a různých dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: ef7d06dfb074a3453f5589284cbdaf079c48d111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933765"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Zřídit propustnost databáze v Azure Cosmos DB

Tento článek vysvětluje, jak zřídit propustnost na databázi v Azure Cosmos DB. Můžete zřídit propustnost pro jeden [kontejner](how-to-provision-container-throughput.md)nebo pro databázi a sdílet propustnost mezi kontejnery v něm. Informace o tom, kdy použít úroveň kontejneru a propustnost úrovně databáze, naleznete [v článku Případy použití pro zřizování propustnost na kontejnerech a databázích.](set-throughput.md) Propustnost na úrovni databáze můžete zřídit pomocí portálu Azure nebo sad Azure Cosmos DB SDK.

## <a name="provision-throughput-using-azure-portal"></a>Zřízení propustnosti pomocí webu Azure Portal

### <a name="sql-core-api"></a><a id="portal-sql"></a>Rozhraní SQL (Core) API

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet Azure Cosmos.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová databáze**. Uveďte následující podrobnosti:

   * Zadejte ID databáze.
   * Vyberte **možnost Zřídit propustnost**.
   * Zadejte propustnost (například 1000 ru).
   * Vyberte **OK**.

    ![Snímek obrazovky s dialogovým oknem Nová databáze](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Zřizování propustnost pomocí Azure CLI nebo PowerShellu

Chcete-li vytvořit databázi se sdílenou propustností,

* [Vytvoření databáze pomocí azure cli](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Vytvoření databáze pomocí prostředí Powershell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Zřízení propustnosti pomocí sady .NET SDK

> [!Note]
> Sady Cosmos SDK pro rozhraní SQL API můžete použít k zajištění propustnost pro všechna rozhraní API. Volitelně můžete použít následující příklad pro Rozhraní API Cassandra také.

### <a name="all-apis"></a><a id="dotnet-all"></a>Všechna rozhraní API

### <a name="net-v2-sdk"></a>Sada SDK v síti V2

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

### <a name="net-v3-sdk"></a>Sada SDK v síti Net V3

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Rozhraní API Cassandra
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
