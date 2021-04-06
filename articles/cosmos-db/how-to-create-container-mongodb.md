---
title: Vytvoření kontejneru v rozhraní Azure Cosmos DB API pro MongoDB
description: Naučte se, jak vytvořit kontejner v rozhraní Azure Cosmos DB API pro MongoDB pomocí Azure Portal, .NET, Java, Node.js a dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a5669b15c041f663605a62ef8d02b206928d0c14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101591"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>Vytvoření kontejneru v rozhraní Azure Cosmos DB API pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Tento článek popisuje různé způsoby, jak vytvořit kontejner v rozhraní Azure Cosmos DB API pro MongoDB. Ukazuje, jak vytvořit kontejner pomocí Azure Portal, Azure CLI, PowerShellu nebo podporovaných sad SDK. Tento článek ukazuje, jak vytvořit kontejner, zadat klíč oddílu a zajistit propustnost.

Tento článek popisuje různé způsoby, jak vytvořit kontejner v rozhraní Azure Cosmos DB API pro MongoDB. Pokud používáte jiné rozhraní API, přečtěte si téma [SQL API](how-to-create-container.md), [rozhraní API Cassandra](how-to-create-container-cassandra.md), [rozhraní Gremlin API](how-to-create-container-gremlin.md)a [rozhraní API pro tabulky](how-to-create-container-table.md) články a vytvořte kontejner.

> [!NOTE]
> Při vytváření kontejnerů se ujistěte, že nevytvoříte dva kontejnery se stejným názvem, ale s různou velikostí písmen. Důvodem je to, že některé části platformy Azure nerozlišují velká a malá písmena a to může vést k nejasnostem/kolizi telemetrie a akcí na kontejnerech s takovými názvy.

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Vytvoření s použitím webu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový kontejner**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte novou databázi, nebo použijte existující.
   * Zadejte ID kontejneru.
   * Zadejte horizontálních oddílů klíč.
   * Zadejte propustnost, která se má zřídit (například 1000 ru).
   * Vyberte **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Snímek obrazovky Azure Cosmos DB API pro MongoDB, dialogové okno Přidat kontejner":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>Vytvoření pomocí sady .NET SDK

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB přenosový protokol nerozumí konceptu [jednotek žádostí](request-units.md). Pro vytvoření nové kolekce s zřízenou propustností použijte sady SDK pro rozhraní SQL Azure Portal nebo Cosmos DB.

Pokud při vytváření kolekce dojde k výjimce časového limitu, proveďte operaci čtení a ověřte, jestli se kolekce úspěšně vytvořila. Operace čtení vyvolá výjimku, dokud není operace vytvoření kolekce úspěšná. Seznam stavových kódů, které podporuje operace vytvořit, najdete v článku [stavové kódy HTTP pro Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Vytvoření pomocí Azure CLI

[Vytvořte kolekci pro Azure Cosmos DB rozhraní API MongoDB pomocí Azure CLI](./scripts/cli/mongodb/create.md). Seznam všech ukázek Azure CLI napříč všemi Azure Cosmos DB rozhraní API najdete v [ukázkách Azure CLI pro Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Vytvoření s použitím PowerShellu

[Vytvořte kolekci pro Azure Cosmos DB rozhraní API MongoDB pomocí PowerShellu](./scripts/powershell/mongodb/create.md). Výpis všech ukázek PowerShellu napříč všemi Azure Cosmos DB rozhraní API najdete v tématu [ukázky PowerShellu](powershell-samples.md) .

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Vytvoření kontejneru pomocí šablon Azure Resource Manager

[Vytvořte kolekci pro Azure Cosmos DB rozhraní API MongoDB pomocí šablony Správce prostředků](./manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput).

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](./account-databases-containers-items.md)