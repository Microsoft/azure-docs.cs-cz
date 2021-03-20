---
title: Vytvoření databáze rozhraní API a kontejneru Core (SQL) pro Azure Cosmos DB
description: Vytvoření databáze rozhraní API a kontejneru Core (SQL) pro Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: e181100d664d02193d248c8f925503523bbaa129
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566839"
---
# <a name="create-an-azure-cosmos-core-sql-api-account-database-and-container-using-azure-cli"></a>Vytvoření účtu rozhraní API pro Azure Cosmos Core (SQL), databáze a kontejneru pomocí Azure CLI
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.9.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/create.sh "Create an Azure Cosmos DB SQL (Core) API account, database, and container.")]

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
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
