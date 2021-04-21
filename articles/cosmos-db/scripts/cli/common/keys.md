---
title: Práce s klíči účtu a připojovacími řetězci pro účet Azure Cosmos
description: Práce s klíči účtu a připojovacími řetězci pro účet Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: bef0501704a3c9c400d998b6e84cf1cabb839dd1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770862"
---
# <a name="work-with-account-keys-and-connection-strings-for-an-azure-cosmos-account-using-azure-cli"></a>Práce s klíči účtu a připojovacími řetězci pro účet Azure Cosmos pomocí Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.9.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

Tento skript ukazuje čtyři operace.

- Vypsat všechny klíče účtu
- Vypsat klíče účtu jen pro čtení
- Vypsat připojovací řetězce
- Opětovné vygenerování klíčů účtu

> [!NOTE]
> Tato ukázka demonstruje použití účtu rozhraní API SQL (Core), ale operace klíče účtu a připojovacího řetězce jsou identické napříč všemi databázovými rozhraními API v Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/keys.sh "Keys and connection string operations for Cosmos DB.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Vytvoří účet služby Azure Cosmos DB. |
| [AZ cosmosdb Keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) | Vypíše klíče pro účet Azure Cosmos DB. |
| [AZ cosmosdb list-Read-Only-Keys](/cli/azure/cosmosdb#az_cosmosdb_list_read_only_keys) | Vypíše klíče jen pro čtení pro účet Azure Cosmos DB. |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb#az_cosmosdb_list_connection_strings) | Vypíše připojovací řetězce pro účet Azure Cosmos DB. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az_cosmosdb_regenerate-key) | Znovu vygenerujte klíče pro účet Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
