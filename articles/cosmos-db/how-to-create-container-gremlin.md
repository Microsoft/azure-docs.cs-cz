---
title: Vytvoření kontejneru v rozhraní Azure Cosmos DB Gremlin API
description: Naučte se, jak vytvořit kontejner ve Azure Cosmos DB rozhraní API Gremlin pomocí Azure Portal, .NET a dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: f7e9de1f23ec46af08fe96b5db3170fac9a7eb2e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101625"
---
# <a name="create-a-container-in-azure-cosmos-db-gremlin-api"></a>Vytvoření kontejneru v rozhraní Azure Cosmos DB Gremlin API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Tento článek popisuje různé způsoby, jak vytvořit kontejner v rozhraní Azure Cosmos DB Gremlin API. Ukazuje, jak vytvořit kontejner pomocí Azure Portal, Azure CLI, PowerShellu nebo podporovaných sad SDK. Tento článek ukazuje, jak vytvořit kontejner, zadat klíč oddílu a zajistit propustnost.

Tento článek popisuje různé způsoby, jak vytvořit kontejner v rozhraní Azure Cosmos DB Gremlin API. Pokud používáte jiné rozhraní API, přečtěte si téma [rozhraní API pro MongoDB](how-to-create-container-mongodb.md), [rozhraní API Cassandra](how-to-create-container-cassandra.md), [rozhraní API pro tabulky](how-to-create-container-table.md)a články [rozhraní SQL API](how-to-create-container.md) k vytvoření kontejneru.

> [!NOTE]
> Při vytváření kontejnerů se ujistěte, že nevytvoříte dva kontejnery se stejným názvem, ale s různou velikostí písmen. Důvodem je to, že některé části platformy Azure nerozlišují velká a malá písmena a to může vést k nejasnostem/kolizi telemetrie a akcí na kontejnerech s takovými názvy.

## <a name="create-using-azure-portal"></a><a id="portal-gremlin"></a>Vytvoření s použitím webu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-graph-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový graf** . Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte novou databázi, nebo použijte existující.
   * Zadejte ID grafu.
   * Vyberte **neomezenou** kapacitu úložiště.
   * Zadejte klíč oddílu pro vrcholy.
   * Zadejte propustnost, která se má zřídit (například 1000 ru).
   * Vyberte **OK** .

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Snímek obrazovky s rozhraním API Gremlin, dialogové okno Přidat graf":::

## <a name="create-using-net-sdk"></a><a id="dotnet-sql-graph"></a>Vytvoření pomocí sady .NET SDK

Pokud při vytváření kolekce dojde k výjimce časového limitu, proveďte operaci čtení a ověřte, jestli se kolekce úspěšně vytvořila. Operace čtení vyvolá výjimku, dokud není operace vytvoření kolekce úspěšná. Seznam stavových kódů, které podporuje operace vytvořit, najdete v článku [stavové kódy HTTP pro Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Vytvoření pomocí Azure CLI

[Vytvoření grafu Gremlin pomocí Azure CLI](./scripts/cli/gremlin/create.md) Seznam všech ukázek Azure CLI napříč všemi Azure Cosmos DB rozhraní API najdete v [ukázkách Azure CLI pro Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Vytvoření s použitím PowerShellu

[Vytvořte graf Gremlin pomocí prostředí PowerShell](./scripts/powershell/gremlin/create.md). Výpis všech ukázek PowerShellu napříč všemi Azure Cosmos DB rozhraní API najdete v tématu [ukázky PowerShellu](powershell-samples.md) .

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](./account-databases-containers-items.md)