---
title: Zřízení propustnosti databáze v Azure Cosmos DB SQL API
description: Naučte se zřídit propustnost na úrovni databáze v Azure Cosmos DB SQL API pomocí Azure Portal, CLI, PowerShellu a různých dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4ecbee2260da735cd6ba74d3b9ffb55b4a652e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341990"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Zřizování standardní (ruční) propustnosti databáze v Azure Cosmos DB-SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek vysvětluje, jak zřídit standardní (ruční) propustnost v databázi v Azure Cosmos DB SQL API. Můžete zřídit propustnost pro jeden [kontejner](how-to-provision-container-throughput.md)nebo pro databázi a sdílet propustnost mezi kontejnery. Informace o použití úrovně kontejneru a propustnosti na úrovni databáze najdete v článku [případy použití pro zajištění propustnosti v kontejnerech a databázích](set-throughput.md) . Propustnost na úrovni databáze můžete zřídit pomocí Azure Portal nebo Azure Cosmos DB SDK.

Pokud používáte jiné rozhraní API, přečtěte si téma [rozhraní API pro MongoDB](how-to-provision-throughput-mongodb.md), [rozhraní API Cassandra](how-to-provision-throughput-cassandra.md), články [Gremlin API](how-to-provision-throughput-gremlin.md) pro zajištění propustnosti.

## <a name="provision-throughput-using-azure-portal"></a>Zřízení propustnosti pomocí webu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet Azure Cosmos.

1. Otevřete podokno **Průzkumník dat** a vyberte možnost **Nová databáze**. Zadejte následující podrobnosti:

   * Zadejte ID databáze.
   * Vyberte možnost **zřídit propustnost databáze** .
   * Zadejte propustnost (například 1000 ru).
   * Vyberte **OK**.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="Snímek obrazovky dialogového okna Nová databáze":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Zajištění propustnosti pomocí Azure CLI nebo PowerShellu

Postup vytvoření databáze se sdílenou propustností najdete v tématu.

* [Vytvoření databáze pomocí Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Vytvoření databáze pomocí prostředí PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Zřízení propustnosti pomocí sady .NET SDK

> [!Note]
> Pomocí sady Azure Cosmos SDK pro SQL API můžete zřídit propustnost pro všechna rozhraní API. Volitelně můžete pro rozhraní API Cassandra také použít následující příklad.

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

## <a name="next-steps"></a>Další kroky

V následujících článcích se dozvíte o zřízené propustnosti v Azure Cosmos DB:

* [Globální škálování zřízené propustnosti](./request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Jak zřídit standardní (ruční) propustnost pro kontejner](how-to-provision-container-throughput.md)
* [Jak zřídit propustnost automatického škálování pro kontejner](how-to-provision-autoscale-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)