---
title: Aktualizace RU/s pro databázi a kontejner rozhraní API SQL (Core) pro Azure Cosmos DB
description: Aktualizace RU/s pro databázi a kontejner rozhraní API SQL (Core) pro Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 23b18d903bfd4e9d723524924e6c31db45c14d62
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87429901"
---
# <a name="update-rus-for-a-sql-core-api-database-and-container-for-azure-cosmos-db-using-azure-cli"></a>Aktualizace RU/s pro databázi a kontejner rozhraní API SQL (Core) pro Azure Cosmos DB pomocí Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.9.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří databázi rozhraní SQL (Core) API se sdílenou propustností a kontejnerem rozhraní API SQL (Core) s vyhrazenou propustností a pak aktualizuje propustnost pro databázi i pro kontejner.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Update RU/s for a SQL database and container.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Vytvoří účet služby Azure Cosmos DB. |
| [AZ cosmosdb SQL Database Create](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Vytvoří databázi SQL Azure Cosmos (jádro). |
| [AZ cosmosdb SQL Container Create](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Vytvoří kontejner SQL Cosmos (jádro) Azure. |
| [AZ cosmosdb SQL Database propustnost Update](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Aktualizace RU/s pro databázi Azure Cosmos SQL (jádro) |
| [AZ cosmosdb SQL Container propustnost Update](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Aktualizujte RU/s pro kontejner Azure Cosmos SQL (jádro). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
