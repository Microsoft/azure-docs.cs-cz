---
title: Operace skriptů Azure CLI pro propustnost (RU/s) pro služby Azure Cosmos DB API pro prostředky MongoDB
description: Operace skriptů Azure CLI pro propustnost (RU/s) pro služby Azure Cosmos DB API pro prostředky MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 54aebb48986b94dbebea400e6b6ee155a54f514b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566210"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db-api-for-mongodb"></a>Operace propustnosti (RU/s) pomocí Azure CLI pro databázi nebo graf pro rozhraní Azure Cosmos DB API pro MongoDB
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.12.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří databázi MongoDB se sdílenou propustností a kolekcí s vyhrazenou propustností a pak aktualizuje propustnost pro obojí. Skript pak migruje z úrovně Standard na propustnost automatického škálování a potom po migraci přečte hodnotu propustnosti automatického škálování.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Throughput operations for Azure Cosmos DB API for MongoDB.")]

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
| [AZ cosmosdb MongoDB Database Create](/cli/azure/cosmosdb/mongodb/database#az-cosmosdb-mongodb-database-create) | Vytvoří databázi rozhraní API Azure Cosmos MongoDB. |
| [AZ cosmosdb MongoDB Collection Create](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Vytvoří kolekci rozhraní MongoDB API pro Azure Cosmos. |
| [AZ cosmosdb MongoDB Database propustnost Update](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-update) | Aktualizujte ru pro databázi rozhraní API Azure Cosmos MongoDB. |
| [AZ cosmosdb MongoDB Collection Update propustnost](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-update) | Aktualizujte ru pro kolekci rozhraní API Azure Cosmos MongoDB. |
| [AZ cosmosdb MongoDB Database propustnost migrace](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-migrate) | Migrace propustnosti databáze. |
| [AZ cosmosdb MongoDB Collection migrace propustnosti](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-migrate) | Migruje propustnost kolekce. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
