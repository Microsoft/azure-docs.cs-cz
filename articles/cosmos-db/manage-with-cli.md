---
title: Spravovat prostředky Azure Cosmos DB pomocí rozhraní příkazového řádku Azure
description: Spravovat účet Azure Cosmos DB, databáze a kontejnerů pomocí Azure CLI.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 82d7cdf0c9519bb8a682445e666d46d6fd7bfbd7
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550940"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Správa prostředků Azure Cosmos pomocí Azure CLI

Následující průvodci popisuje běžné příkazy pro automatizaci správy účtů služby Azure Cosmos DB, databáze a kontejnerů pomocí Azure CLI. Stránky s referenčními informacemi pro všechny příkazy rozhraní příkazového řádku Azure Cosmos DB jsou k dispozici v [referenčních informacích k Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Můžete také najít další příklady v [ukázky v Azure CLI pro službu Azure Cosmos DB](cli-samples.md), jak vytvářet a spravovat účty služby Cosmos DB, databáze a kontejnerů pro MongoDB, Gremlin, Cassandra a Table API.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Konzistence typu relace v oblastech USA – východ a USA – Západ, chcete-li vytvořit účet služby Azure Cosmos DB s rozhraním SQL API, spusťte následující příkaz:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Název účtu Azure Cosmos musí obsahovat malá písmena.

## <a name="create-a-database"></a>Vytvoření databáze

Chcete-li vytvořit databázi Cosmos DB, spusťte následující příkaz:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Vytvoření kontejneru

K vytvoření kontejneru Cosmos DB s RU/s 400 a klíč oddílu, spusťte následující příkaz:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>Změňte propustnost kontejneru

Chcete-li změnit propustnosti kontejneru Cosmos DB na 1000 RU/s, spusťte následující příkaz:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>Vypsat klíče účtu

Získat klíče účtu Cosmos, spusťte následující příkaz:

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Seznam připojovacích řetězců

Pokud chcete získat připojovací řetězce pro váš účet Cosmos, spusťte následující příkaz:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Znovu vygenerovat klíč účtu

Znovu vygenerovat nový primární klíč pro svůj účet Cosmos, spusťte následující příkaz:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v tématu:

- [Instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)
- [Referenční informace k rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Další ukázky Azure CLI pro službu Azure Cosmos DB](cli-samples.md)
