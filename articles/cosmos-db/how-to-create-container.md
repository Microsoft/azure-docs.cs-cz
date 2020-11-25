---
title: Vytvoření kontejneru v Azure Cosmos DB SQL API
description: Naučte se, jak vytvořit kontejner v Azure Cosmos DB SQL API pomocí Azure Portal, .NET, Java, Pythonu, Node.js a dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 302c5d6e8e523a11b8773f10bb6089e3bea09bdd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006844"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Vytvoření kontejneru v Azure Cosmos DB SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek popisuje různé způsoby, jak vytvořit kontejner v rozhraní Azure Cosmos DB SQL API. Ukazuje, jak vytvořit kontejner pomocí Azure Portal, Azure CLI, PowerShellu nebo podporovaných sad SDK. Tento článek ukazuje, jak vytvořit kontejner, zadat klíč oddílu a zajistit propustnost.

Tento článek popisuje různé způsoby, jak vytvořit kontejner v rozhraní Azure Cosmos DB SQL API. Pokud používáte jiné rozhraní API, přečtěte si téma [rozhraní API pro MongoDB](how-to-create-container-mongodb.md), [rozhraní API Cassandra](how-to-create-container-cassandra.md), [rozhraní Gremlin API](how-to-create-container-gremlin.md)a [rozhraní API pro tabulky](how-to-create-container-table.md) články k vytvoření kontejneru.

> [!NOTE]
> Při vytváření kontejnerů se ujistěte, že nevytvoříte dva kontejnery se stejným názvem, ale s různou velikostí písmen. Důvodem je to, že některé části platformy Azure nerozlišují velká a malá písmena a to může vést k nejasnostem/kolizi telemetrie a akcí na kontejnerech s takovými názvy.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Vytvoření kontejneru pomocí webu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte **Nový kontejner**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte novou databázi, nebo použijte existující.
   * Zadejte ID kontejneru.
   * Zadejte klíč oddílu.
   * Zadejte propustnost, která se má zřídit (například 1000 ru).
   * Vyberte **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Snímek obrazovky s podoknem Průzkumník dat s zvýrazněným novým kontejnerem":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Vytvoření kontejneru pomocí Azure CLI

[Vytvořte kontejner pomocí Azure CLI](manage-with-cli.md#create-a-container). Seznam všech ukázek Azure CLI napříč všemi Azure Cosmos DB rozhraní API najdete v [ukázkách Azure CLI pro Azure Cosmos DB](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>Vytvoření kontejneru pomocí PowerShellu

[Vytvořte kontejner pomocí PowerShellu](manage-with-powershell.md#create-container). Výpis všech ukázek PowerShellu napříč všemi Azure Cosmos DB rozhraní API najdete v tématu [ukázky PowerShellu](powershell-samples.md) .

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Vytvoření kontejneru pomocí sady .NET SDK

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

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](./account-databases-containers-items.md)