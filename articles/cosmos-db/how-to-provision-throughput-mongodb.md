---
title: Zřízení propustnosti v rozhraní Azure Cosmos DB API pro prostředky MongoDB
description: Naučte se, jak zajistit propustnost kontejnerů, databází a automatického škálování v Azure Cosmos DB API pro prostředky MongoDB. Budete používat Azure Portal, CLI, PowerShell a různé další sady SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 25eaa4a66fb4a73f976edbd30e6f82015ce84f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086121"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>Zřizování databáze, kontejneru nebo propustnosti automatického škálování v rozhraní Azure Cosmos DB API pro prostředky MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Tento článek vysvětluje, jak zřídit propustnost v rozhraní Azure Cosmos DB API pro MongoDB. Můžete zřídit standardní (ruční) nebo propustnost automatického škálování na kontejneru nebo databázi a sdílet ji mezi kontejnery v rámci databáze. Propustnost můžete zřídit pomocí Azure Portal, rozhraní příkazového řádku Azure nebo sady Azure Cosmos DB SDK.

Pokud používáte jiné rozhraní API, přečtěte si téma [SQL API](how-to-provision-container-throughput.md), [rozhraní API Cassandra](how-to-provision-throughput-cassandra.md), články [rozhraní Gremlin API](how-to-provision-throughput-gremlin.md) a zajistěte propustnost.

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)nebo vyberte existující účet Azure Cosmos.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nová kolekce**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte novou databázi, nebo použijte existující. Vyberte možnost **zřízení propustnosti databáze** , pokud chcete zřídit propustnost na úrovni databáze.
   * Zadejte ID kolekce.
   * Zadejte hodnotu klíče oddílu (například `/ItemID` ).
   * Zadejte propustnost, kterou chcete zřídit (například 1000 ru).
   * Vyberte **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="Snímek obrazovky Průzkumník dat při vytváření nové kolekce s propustností úrovně databáze":::

> [!Note]
> Pokud zřizujete propustnost na kontejneru v účtu Azure Cosmos, který je nakonfigurovaný s rozhraním API Azure Cosmos DB pro MongoDB, použijte `/myShardKey` pro cestu ke klíči oddílu.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> SADA .NET SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Šablony Azure Resource Manager lze použít ke zřízení propustnosti automatického škálování pro databáze nebo prostředky na úrovni kontejneru pro všechna rozhraní API Azure Cosmos DB. Ukázky najdete v tématu [šablony Azure Resource Manager pro Azure Cosmos DB](templates-samples-mongodb.md) .

## <a name="azure-cli"></a>Azure CLI

Azure CLI se dá využít ke zřízení propustnosti automatického škálování u databáze nebo prostředků na úrovni kontejneru pro všechna Azure Cosmos DB rozhraní API. Ukázky najdete v [ukázkách Azure CLI pro Azure Cosmos DB](cli-samples-mongodb.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell lze použít ke zřízení propustnosti automatického škálování u databáze nebo prostředků na úrovni kontejneru pro všechna rozhraní API Azure Cosmos DB. Ukázky najdete v tématu [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples-mongodb.md).

## <a name="next-steps"></a>Další kroky

Informace o zřizování propustnosti v Azure Cosmos DB najdete v následujících článcích:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)