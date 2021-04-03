---
title: Operace skriptů Azure CLI pro propustnost (RU/s) pro prostředky Azure Cosmos DB rozhraní API pro tabulky
description: Operace skriptů Azure CLI pro propustnost (RU/s) pro prostředky Azure Cosmos DB rozhraní API pro tabulky
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 6e76da71cb14122817090e64354babf5a618db8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94562657"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-table-for-azure-cosmos-db-table-api"></a>Operace propustnosti (RU/s) pomocí rozhraní příkazového řádku Azure pro tabulku Azure Cosmos DB rozhraní API pro tabulky
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.12.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří tabulku rozhraní API pro tabulky a pak aktualizuje propustnost tabulky. Skript pak migruje z úrovně Standard na propustnost automatického škálování a potom po migraci přečte hodnotu propustnosti automatického škálování.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/throughput.sh "Throughput operations for Table API.")]

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
| [AZ cosmosdb Table propustnost Update](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-update) | Aktualizujte propustnost pro rozhraní API pro tabulky tabulku Azure Cosmos. |
| [AZ cosmosdb Table propustnost migrace](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-migrate) | Migrace propustnosti pro službu Azure Cosmos rozhraní API pro tabulky Table |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
