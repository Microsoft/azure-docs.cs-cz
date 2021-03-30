---
title: Vytvoření kontejneru v Azure Cosmos DB rozhraní API Cassandra
description: Naučte se, jak vytvořit kontejner v Azure Cosmos DB rozhraní API Cassandra pomocí Azure Portal, .NET, Java, Pythonu, Node.js a dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 01030a563c15eee7786058c2eae30d23803dfe42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101642"
---
# <a name="create-a-container-in-azure-cosmos-db-cassandra-api"></a>Vytvoření kontejneru v Azure Cosmos DB rozhraní API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Tento článek popisuje různé způsoby, jak vytvořit kontejner v Azure Cosmos DB rozhraní API Cassandra. Ukazuje, jak vytvořit kontejner pomocí Azure Portal, Azure CLI, PowerShellu nebo podporovaných sad SDK. Tento článek ukazuje, jak vytvořit kontejner, zadat klíč oddílu a zajistit propustnost.

Tento článek popisuje různé způsoby, jak vytvořit kontejner v Azure Cosmos DB rozhraní API Cassandra. Pokud používáte jiné rozhraní API, přečtěte si článek [rozhraní API pro MongoDB](how-to-create-container-mongodb.md), [Gremlin API](how-to-create-container-gremlin.md), [rozhraní API pro tabulky](how-to-create-container-table.md)a [SQL API](how-to-create-container.md) a vytvořte kontejner.

> [!NOTE]
> Při vytváření kontejnerů se ujistěte, že nevytvoříte dva kontejnery se stejným názvem, ale s různou velikostí písmen. Důvodem je to, že některé části platformy Azure nerozlišují velká a malá písmena a to může vést k nejasnostem/kolizi telemetrie a akcí na kontejnerech s takovými názvy.

## <a name="create-using-azure-portal"></a><a id="portal-cassandra"></a>Vytvoření s použitím webu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte možnost **Nová tabulka**. Dále zadejte následující podrobnosti:

   * Určete, zda vytváříte nové místo na disku, nebo použijte existující.
   * Zadejte název tabulky.
   * Zadejte vlastnosti a zadejte primární klíč.
   * Zadejte propustnost, která se má zřídit (například 1000 ru).
   * Vyberte **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Snímek obrazovky rozhraní API Cassandra, dialogové okno Přidat tabulku":::

> [!NOTE]
> V případě rozhraní API Cassandra se jako klíč oddílu používá primární klíč.

## <a name="create-using-net-sdk"></a><a id="dotnet-cassandra"></a>Vytvoření pomocí sady .NET SDK

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

Pokud při vytváření kolekce dojde k výjimce časového limitu, proveďte operaci čtení a ověřte, jestli se kolekce úspěšně vytvořila. Operace čtení vyvolá výjimku, dokud není operace vytvoření kolekce úspěšná. Seznam stavových kódů, které podporuje operace vytvořit, najdete v článku [stavové kódy HTTP pro Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Vytvoření pomocí Azure CLI

[Vytvořte tabulku Cassandra pomocí Azure CLI](./scripts/cli/cassandra/create.md). Seznam všech ukázek Azure CLI napříč všemi Azure Cosmos DB rozhraní API najdete v [ukázkách Azure CLI pro Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Vytvoření s použitím PowerShellu

[Vytvořte tabulku Cassandra pomocí prostředí PowerShell](./scripts/powershell/cassandra/create.md). Výpis všech ukázek PowerShellu napříč všemi Azure Cosmos DB rozhraní API najdete v tématu [ukázky PowerShellu](powershell-samples.md) .

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](./account-databases-containers-items.md)