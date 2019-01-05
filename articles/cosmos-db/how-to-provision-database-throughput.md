---
title: Zřízení propustnosti databáze ve službě Azure Cosmos DB
description: Zjistěte, jak zřídit propustnost na úrovni databáze ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 7c253141e0e6e76f845d08e68a1d79949fe811e8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034229"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Zřízení propustnosti pro databázi ve službě Azure Cosmos DB

Tento článek vysvětluje, jak zřídit propustnost pro kontejner databázi ve službě Azure Cosmos DB. Propustnost můžete zřídit pro jeden [kontejner](how-to-provision-container-throughput.md) nebo pro databázi a sdílet ji mezi kontejnery v rámci databáze. Propustnost na úrovni databáze můžete zřídit pomocí webu Azure Portal nebo sad SDK služby Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Zřízení propustnosti pomocí webu Azure Portal

### <a id="portal-sql"></a>Rozhraní SQL (Core) API

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet služby Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová databáze**. Potom ve formuláři vyplňte následující podrobnosti:

   * Zadejte ID databáze. 
   * Vyberte Zřídit propustnost.
   * Zadejte propustnost, například 1 000 RU.
   * Vyberte **OK**.

![Zřízení propustnosti databáze v rozhraní SQL API](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Zřízení propustnosti pomocí sady .NET SDK

> [!Note]
> Propustnost pro všechna rozhraní API můžete zřídit pomocí rozhraní SQL API. Volitelně můžete použít také následující příklad pro rozhraní API Cassandra.

### <a id="dotnet-all"></a>Všechna rozhraní API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>Rozhraní API Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>Další postup

V následujících článcích najdete informace o zřizování propustnosti ve službě Cosmos DB:

* [Zřízení propustnosti pro kontejner](how-to-provision-container-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
