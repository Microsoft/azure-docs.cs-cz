---
title: Aktualizace RU/s pro klíčový prostor Cassandra a tabulka pro Azure Cosmos DB
description: Aktualizace RU/s pro Cassandra keyspace a tabulka pro Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 3615157543d826050b8adf0e6ae59bf62ad9cb1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71827302"
---
# <a name="update-rus-for-a-cassandra-keyspace-and-table-using-azure-cli"></a>Aktualizace RU/s pro keyspace a tabulka Cassandra pomocí rozhraní příkazového příkazu Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, toto téma vyžaduje, abyste spouštěli Azure CLI verze 2.0.73 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří prostor klíčů Cassandra se sdílenou propustností a tabulku Cassandra s vyhrazenou propustností a poté aktualizuje propustnost pro keyspace i tabulku.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Update RU/s for Cassandra keyspace and table.")]

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
| [az cosmosdb cassandra keyspace vytvořit](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Vytvoří klíčový prostor Azure Cosmos Cassandra. |
| [az cosmosdb cassandra tabulka vytvořit](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Vytvoří tabulku Azure Cosmos Cassandra. |
| [Az cosmosdb cassandra aktualizace propustnost keyspace](/cli/azure/cosmosdb/cassandra/keyspace/throughput?view=azure-cli-latest#az-cosmosdb-cassandra-keyspace-throughput-update) | Aktualizujte RU/s pro klíčový prostor Azure Cosmos Cassandra. |
| [Az cosmosdb cassandra aktualizace propustnost tabulky](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Aktualizujte RU/s pro tabulku Azure Cosmos Cassandra. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o cli DB Azure Cosmos najdete v [dokumentaci k příkazu KONT db Azure Cosmos](/cli/azure/cosmosdb)DB .

Všechny ukázky skriptu Rozhraní příkazového od Db BZI Azure Cosmos najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
