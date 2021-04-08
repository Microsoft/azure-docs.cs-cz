---
title: Vytvoření tabulky rozhraní API pro tabulky pro Azure Cosmos DB
description: Vytvoření tabulky rozhraní API pro tabulky pro Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: c6efc013241925085ecc9a5e3ead804ba3e82dee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94565955"
---
# <a name="create-an-azure-cosmos-table-api-account-and-table-using-azure-cli"></a>Vytvoření účtu a tabulky Azure Cosmos rozhraní API pro tabulky pomocí Azure CLI
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.9.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/create.sh "Create an Azure Cosmos DB Table API account and table.")]

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
| [AZ cosmosdb Table Create](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | Vytvoří tabulku rozhraní API pro tabulky služby Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
