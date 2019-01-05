---
title: Spravovat prostředky Azure Cosmos DB pomocí rozhraní příkazového řádku Azure
description: Spravovat účet Azure Cosmos DB, databáze a kontejnerů pomocí Azure CLI.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: d45f5b5c5945796e30c86b2e3ef48d6b8e693b99
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038972"
---
# <a name="manage-azure-cosmos-db-resources-using-azure-cli"></a>Spravovat prostředky Azure Cosmos DB pomocí rozhraní příkazového řádku Azure

Následující průvodci popisuje příkazy, které automatizují správu účtů služby Azure Cosmos DB, databáze a kontejnerů pomocí Azure CLI. Zahrnuje také příkazy pro škálování propustnosti kontejnerů. Stránky s referenčními informacemi pro všechny příkazy rozhraní příkazového řádku Azure Cosmos DB jsou k dispozici v [referenčních informacích k Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Můžete také najít další příklady v [ukázky v Azure CLI pro službu Azure Cosmos DB](cli-samples.md), jak vytvářet a spravovat účty služby Cosmos DB, databáze a kontejnerů pro MongoDB, Gremlin, Cassandra a Table API.

Tento ukázkový skript rozhraní příkazového řádku vytvoří účet, databázi a kontejner rozhraní SQL API služby Azure Cosmos DB.  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Vytvoření účtu služby Azure Cosmos DB pomocí rozhraní SQL API, konzistence typu relace, více hlavních databází povolené v oblastech východní USA a západní USA, otevřete rozhraní příkazového řádku Azure nebo cloud shell a spusťte následující příkaz:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>Vytvoření databáze

Chcete-li vytvořit databázi Cosmos DB, otevřete rozhraní příkazového řádku Azure nebo cloud shell a spusťte následující příkaz:

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>Vytvoření kontejneru

Otevřete rozhraní příkazového řádku Azure k vytvoření kontejneru Cosmos DB s 1 000 RU/s a klíč oddílu, nebo cloud shell a spusťte následující příkaz:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>Změňte propustnost kontejneru

Chcete-li změnit propustnosti kontejneru Cosmos DB na RU/s 400, otevřete rozhraní příkazového řádku Azure nebo cloud shell a spusťte následující příkaz:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>Vypsat klíče účtu

Při vytváření účtu služby Azure Cosmos DB, generuje tato služba dva hlavní přístupové klíče, které se dá použít pro ověření při přístupu k účtu Azure Cosmos DB. Poskytnutím dvou přístupových klíčů služby Azure Cosmos DB umožňuje znovu vygenerovat klíče bez přerušení ke svému účtu Azure Cosmos DB. Klíče jen pro čtení pro ověřování jen pro čtení operace jsou také k dispozici. (Primární i sekundární) existují dva klíče pro čtení i zápis (primární i sekundární) a dva klíče jen pro čtení. Spuštěním následujícího příkazu můžete získat klíče pro váš účet:

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>Seznam připojovacích řetězců

Připojovací řetězec pro připojení aplikace k účtu služby Cosmos DB se dá načíst pomocí následujícího příkazu.

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>Znovu vygenerovat klíč účtu

Měli byste změnit přístupové klíče ke svému účtu Azure Cosmos DB pravidelně, aby lépe zabezpečit vaše připojení. Dva přístupové klíče jsou přiřazeny vám umožní spravovat připojení k účtu Azure Cosmos DB používat jeden přístupový klíč, zatímco znovu vygenerujete druhý přístupový klíč.

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v tématu:

- [Instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)
- [Referenční informace k rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Další ukázky Azure CLI pro službu Azure Cosmos DB](cli-samples.md)
