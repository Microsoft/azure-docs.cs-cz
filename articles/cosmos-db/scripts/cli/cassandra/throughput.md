---
title: Operace skriptů Azure CLI pro propustnost (RU/s) pro prostředky Azure Cosmos DB rozhraní API Cassandra
description: Operace skriptů Azure CLI pro propustnost (RU/s) pro prostředky Azure Cosmos DB rozhraní API Cassandra
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 1979c59af53ebeccdbd7c910a87fb4c2536fe403
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94565575"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Operace propustnosti (RU/s) pomocí rozhraní příkazového řádku Azure pro místo nebo tabulku Azure Cosmos DB-rozhraní API Cassandra
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.12.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří Cassandra místo na disku se sdílenou propustností a tabulkou Cassandra s vyhrazenou propustností a pak aktualizuje propustnost pro prostor klíčů i tabulku. Skript pak migruje z úrovně Standard na propustnost automatického škálování a potom po migraci přečte hodnotu propustnosti automatického škálování.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Throughput operations for Cassandra keyspace and table.")]

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
| [AZ cosmosdb Cassandra mezerník Space Create](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Vytvoří Cosmos prostor Cassandra pro Azure. |
| [AZ cosmosdb Cassandra Table Create](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Vytvoří tabulku Azure Cosmos Cassandra. |
| [AZ cosmosdb Cassandra Space propustnost Update](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az-cosmosdb-cassandra-keyspace-throughput-update) | Aktualizujte RU/s pro Cosmos prostor Cassandra pro Azure. |
| [AZ cosmosdb Cassandra Table propustnost Update](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Aktualizujte RU/s pro tabulku Azure Cosmos Cassandra. |
| [AZ cosmosdb Cassandra Space propustnost migrace](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az_cosmosdb_cassandra_keyspace_throughput_migrate) | Migruje propustnost pro Cosmos prostor Cassandra pro Azure. |
| [AZ cosmosdb Cassandra Table propustnost migrace](/cli/azure/cosmosdb/cassandra/table/throughput#az_cosmosdb_cassandra_table_throughput_migrate) | Migruje propustnost pro tabulku Azure Cosmos Cassandra. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
