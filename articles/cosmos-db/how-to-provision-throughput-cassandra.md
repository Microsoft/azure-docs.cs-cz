---
title: Zřízení propustnosti u prostředků Azure Cosmos DB rozhraní API Cassandra
description: Naučte se, jak zajistit propustnost kontejnerů, databází a automatického škálování v Azure Cosmos DB rozhraní API Cassandrach prostředcích. Budete používat Azure Portal, CLI, PowerShell a různé další sady SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 63b633ed67c03a006a154bc69a1aafb4cb4aa6d0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086274"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Zřizování databáze, kontejneru nebo propustnosti automatického škálování na Azure Cosmos DB rozhraní API Cassandra prostředků
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Tento článek vysvětluje, jak zajistit propustnost v Azure Cosmos DB rozhraní API Cassandra. Můžete zřídit standardní (ruční) nebo propustnost automatického škálování na kontejneru nebo databázi a sdílet ji mezi kontejnery v rámci databáze. Propustnost můžete zřídit pomocí Azure Portal, rozhraní příkazového řádku Azure nebo sady Azure Cosmos DB SDK.

Pokud používáte jiné rozhraní API, přečtěte si téma [SQL API](how-to-provision-container-throughput.md), [API pro MongoDB](how-to-provision-throughput-mongodb.md), články [Gremlin API](how-to-provision-throughput-gremlin.md) , které vám pomohou zajistit propustnost.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)nebo vyberte existující účet Azure Cosmos.

1. Otevřete podokno **Průzkumník dat** a vyberte možnost **Nová tabulka** . Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte nový prostor pro nové místo, nebo použijte existující. Vyberte možnost **zřízení propustnosti databáze** , pokud chcete zřídit propustnost na úrovni prostoru.
   * V příkazu CQL zadejte ID tabulky.
   * Zadejte hodnotu primárního klíče (například `/userrID` ).
   * Zadejte propustnost, kterou chcete zřídit (například 1000 ru).
   * Vyberte **OK** .

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="Snímek obrazovky Průzkumník dat při vytváření nové kolekce s propustností úrovně databáze":::

> [!Note]
> Pokud zřizujete propustnost na kontejneru v účtu Azure Cosmos nakonfigurovaném pomocí rozhraní API Cassandra, použijte `/myPrimaryKey` pro cestu ke klíči oddílu.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> SADA .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>Zajištění propustnosti pro tabulku Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
Podobné příkazy lze vydat prostřednictvím libovolného ovladače kompatibilního s CQL.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Změna nebo změna propustnosti pro tabulku Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

Podobný příkaz lze provést prostřednictvím libovolného ovladače kompatibilního s CQL.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Šablony Azure Resource Manager lze použít ke zřízení propustnosti automatického škálování pro databáze nebo prostředky na úrovni kontejneru pro všechna rozhraní API Azure Cosmos DB. Ukázky najdete v tématu [šablony Azure Resource Manager pro Azure Cosmos DB](templates-samples-cassandra.md) .

## <a name="azure-cli"></a>Azure CLI

Azure CLI se dá využít ke zřízení propustnosti automatického škálování u databáze nebo prostředků na úrovni kontejneru pro všechna Azure Cosmos DB rozhraní API. Ukázky najdete v [ukázkách Azure CLI pro Azure Cosmos DB](cli-samples-cassandra.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell lze použít ke zřízení propustnosti automatického škálování u databáze nebo prostředků na úrovni kontejneru pro všechna rozhraní API Azure Cosmos DB. Ukázky najdete v tématu [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples-cassandra.md).

## <a name="next-steps"></a>Další kroky

Informace o zřizování propustnosti v Azure Cosmos DB najdete v následujících článcích:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)