---
title: Aktualizace RU/s pro Cassandra prostor a tabulku pro Azure Cosmos DB
description: Aktualizace RU/s pro Cassandra prostoru a tabulky pro Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 3615157543d826050b8adf0e6ae59bf62ad9cb1b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827302"
---
# <a name="update-rus-for-a-cassandra-keyspace-and-table-using-azure-cli"></a>Aktualizace RU/s pro Cassandra prostor a tabulku pomocí Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.0.73 nebo novější. Pokud chcete zjistit verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří Cassandra místo na disku se sdílenou propustností a tabulkou Cassandra s vyhrazenou propustností a pak aktualizuje propustnost pro prostor klíčů i tabulku.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Update RU/s for Cassandra keyspace and table.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na dokumentaci konkrétního příkazu.

| Příkaz | Poznámky |
|---|---|
| [AZ Group Create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které jsou uložené všechny prostředky. |
| [AZ cosmosdb Create](/cli/azure/cosmosdb#az-cosmosdb-create) | Vytvoří účet Azure Cosmos DB. |
| [AZ cosmosdb Cassandra mezerník Space Create](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Vytvoří Cosmos prostor Cassandra pro Azure. |
| [AZ cosmosdb Cassandra Table Create](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Vytvoří tabulku Azure Cosmos Cassandra. |
| [AZ cosmosdb Cassandra Space propustnost Update](/cli/azure/cosmosdb/cassandra/keyspace/throughput?view=azure-cli-latest#az-cosmosdb-cassandra-keyspace-throughput-update) | Aktualizujte RU/s pro Cosmos prostor Cassandra pro Azure. |
| [AZ cosmosdb Cassandra Table propustnost Update](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Aktualizujte RU/s pro tabulku Azure Cosmos Cassandra. |
| [AZ Group DELETE](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
