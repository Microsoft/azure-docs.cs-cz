---
title: Operace skriptů Azure CLI pro propustnost (RU/s) pro Azure Cosmos DB prostředky rozhraní API Gremlin
description: Operace skriptů Azure CLI pro propustnost (RU/s) pro Azure Cosmos DB prostředky rozhraní API Gremlin
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: ac8f915a1e9cc73c076879ea8e5ee487c3fe0c3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770628"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Operace propustnosti (RU/s) pomocí Azure CLI pro databázi nebo graf pro rozhraní API Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.12.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří databázi Gremlin se sdílenou propustností a grafem Gremlin s vyhrazenou propustností a pak aktualizuje propustnost pro databázi i graf. Skript pak migruje z úrovně Standard na propustnost automatického škálování a potom po migraci přečte hodnotu propustnosti automatického škálování.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Throughput operations for a Gremlin database and graph.")]

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
| [AZ cosmosdb Gremlin Database Create](/cli/azure/cosmosdb/gremlin/database#az_cosmosdb_gremlin_database_create) | Vytvoří databázi Azure Cosmos Gremlin. |
| [AZ cosmosdb Gremlin Graph Create](/cli/azure/cosmosdb/gremlin/graph#az_cosmosdb_gremlin_graph_create) | Vytvoří graf Azure Cosmos Gremlin. |
| [AZ cosmosdb Gremlin Database propustnost Update](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_update) | Aktualizujte RU/s pro databázi Azure Cosmos Gremlin. |
| [AZ cosmosdb Gremlin Graph propustnost Update](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_update) | Aktualizujte RU/s pro Azure Cosmos Gremlin Graph. |
| [AZ cosmosdb Gremlin Database propustnost migrace](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_migrate) | Migruje propustnost databáze Azure Cosmos Gremlin. |
| [AZ cosmosdb Gremlin Graph propustnost migrace](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_migrate) | Migruje propustnost pro graf Azure Cosmos Gremlin. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
