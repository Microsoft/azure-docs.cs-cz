---
title: Vytvoření kontejneru v Azure Cosmos DB rozhraní API pro tabulky
description: Naučte se, jak vytvořit kontejner v Azure Cosmos DB rozhraní API pro tabulky pomocí Azure Portal, .NET, Java, Pythonu, Node.js a dalších sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: fde3145e7bd7f4e53ae7a0c44f02e066c28ec785
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101574"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Vytvoření kontejneru v Azure Cosmos DB rozhraní API pro tabulky
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Tento článek popisuje různé způsoby, jak vytvořit kontejner v Azure Cosmos DB rozhraní API pro tabulky. Ukazuje, jak vytvořit kontejner pomocí Azure Portal, Azure CLI, PowerShellu nebo podporovaných sad SDK. Tento článek ukazuje, jak vytvořit kontejner, zadat klíč oddílu a zajistit propustnost.

Tento článek popisuje různé způsoby, jak vytvořit kontejner v Azure Cosmos DB rozhraní API pro tabulky. Pokud používáte jiné rozhraní API, přečtěte si téma [rozhraní API pro MongoDB](how-to-create-container-mongodb.md), [rozhraní API Cassandra](how-to-create-container-cassandra.md), [rozhraní Gremlin API](how-to-create-container-gremlin.md)a články k [rozhraní SQL API](how-to-create-container.md) k vytvoření kontejneru.

> [!NOTE]
> Při vytváření kontejnerů se ujistěte, že nevytvoříte dva kontejnery se stejným názvem, ale s různou velikostí písmen. Důvodem je to, že některé části platformy Azure nerozlišují velká a malá písmena a to může vést k nejasnostem/kolizi telemetrie a akcí na kontejnerech s takovými názvy.

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Vytvoření s použitím webu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-table-dotnet.md#create-a-database-account)nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte možnost **Nová tabulka**. Dále zadejte následující podrobnosti:

   * Zadejte ID tabulky.
   * Zadejte propustnost, která se má zřídit (například 1000 ru).
   * Vyberte **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Snímek obrazovky rozhraní API pro tabulky, dialogové okno Přidat tabulku":::

> [!Note]
> V případě rozhraní API pro tabulky se klíč oddílu zadává při každém přidání nového řádku.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Vytvoření pomocí Azure CLI

[Vytvoří rozhraní API pro tabulkyovou tabulku pomocí Azure CLI](./scripts/cli/table/create.md). Seznam všech ukázek Azure CLI napříč všemi Azure Cosmos DB rozhraní API najdete v [ukázkách Azure CLI pro Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Vytvoření s použitím PowerShellu

[Vytvořte rozhraní API pro tabulkyovou tabulku pomocí prostředí PowerShell](./scripts/powershell/table/create.md). Výpis všech ukázek PowerShellu napříč všemi Azure Cosmos DB rozhraní API najdete v tématu [ukázky PowerShellu](powershell-samples.md) .

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](./account-databases-containers-items.md)