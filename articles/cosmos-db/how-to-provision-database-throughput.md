---
title: Zřízení propustnosti databáze ve službě Azure Cosmos DB
description: Zjistěte, jak zřídit propustnost na úrovni databáze ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: da56c06e215e02ee3eefe3d0552c962a8c59011e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61054311"
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

## <a name="provision-throughput-using-net-sdk"></a>Zřízení propustnosti pomocí sady .NET SDK

> [!Note]
> Pro všechna rozhraní API můžete zřídit propustnost pomocí sady SDK Cosmos pro rozhraní SQL API. Volitelně můžete v následujícím příkladu pro Apache Cassandra API také.

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

Naleznete v následujících článcích se dozvíte o zřízenou propustnost v databázi Azure Cosmos DB:

* [Globálně škálujte zřízená propustnost](scaling-throughput.md)
* [Zřizování propustnosti na kontejnerech a databází](set-throughput.md)
* [Zřízení propustnosti pro kontejner](how-to-provision-container-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)