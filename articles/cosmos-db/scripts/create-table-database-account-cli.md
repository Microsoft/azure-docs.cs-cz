---
title: Skript Azure CLI – Vytvoření účtu, databáze a tabulky rozhraní API pro tabulky služby Azure Cosmos DB | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření účtu, databáze a tabulky rozhraní API pro tabulky služby Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 178ecfe6971a47009781d8866986d936f8e3a5d2
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019538"
---
# <a name="azure-cosmos-db-create-a-table-api-account-using-azure-cli"></a>Azure Cosmos DB: Vytvoření účtu rozhraní API pro tabulky pomocí Azure CLI

Tento ukázkový skript rozhraní příkazového řádku vytvoří účet, databázi a tabulku rozhraní API pro tabulky služby Azure Cosmos DB.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-table-account/create-cosmosdb-table-account.sh "Create an Azure Cosmos DB Table API account, database, and table.")]

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
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | Vytvoří databázi Azure Cosmos DB. |
| [az cosmosdb collection create](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-create) | Vytvoří tabulku Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku Azure Cosmos DB najdete v [dokumentaci k rozhraní příkazového řádku Azure Cosmos DB](../cli-samples.md).
