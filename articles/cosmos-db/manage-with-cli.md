---
title: Správa prostředků Azure Cosmos DB pomocí Azure CLI
description: Pomocí Azure CLI můžete spravovat Azure Cosmos DB účet, databázi a kontejnery.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 9ec049311fc158b13bba45deb2974d7cdd531f90
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815044"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Správa prostředků Azure Cosmos pomocí Azure CLI

Následující příručka popisuje běžné příkazy pro automatizaci správy Azure Cosmos DB účtů, databází a kontejnerů pomocí Azure CLI. Stránky s referenčními informacemi pro všechny příkazy rozhraní příkazového řádku Azure Cosmos DB jsou k dispozici v [referenčních informacích k Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Další příklady najdete v [ukázkách Azure CLI pro Azure Cosmos DB](cli-samples.md), včetně toho, jak vytvářet a spravovat Cosmos DB účty, databáze a kontejnery pro MongoDB, Gremlin, Cassandra a rozhraní API pro tabulky.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Chcete-li vytvořit účet Azure Cosmos DB pomocí rozhraní SQL API, konzistence relace v oblasti Východní USA a Západní USA, spusťte následující příkaz:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount # must be lowercase and < 31 characters \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Název účtu Azure Cosmos musí být malými písmeny.

## <a name="create-a-database"></a>Vytvoření databáze

Chcete-li vytvořit databázi Cosmos DB, spusťte následující příkaz:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Vytvoření kontejneru

Pokud chcete vytvořit kontejner Cosmos DB s RU/s 400 a klíčem oddílu, spusťte následující příkaz:

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

## <a name="change-the-throughput-of-a-container"></a>Změna propustnosti kontejneru

Chcete-li změnit propustnost Cosmos DB kontejneru na 1000 RU/s, spusťte následující příkaz:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>Výpis klíčů účtu

Pro získání klíčů pro účet Cosmos spusťte následující příkaz:

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Vypsat připojovací řetězce

Pokud chcete získat připojovací řetězce pro svůj účet Cosmos, spusťte následující příkaz:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Znovu vygenerovat klíč účtu

Pokud chcete pro svůj účet Cosmos znovu vygenerovat nový primární klíč, spusťte následující příkaz:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Další postup

Další informace o rozhraní příkazového řádku Azure najdete v těchto tématech:

- [Instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)
- [Referenční informace k rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Další ukázky v Azure CLI pro Azure Cosmos DB](cli-samples.md)
