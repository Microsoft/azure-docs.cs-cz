---
title: Aktualizace RU/s pro databázi Gremlin a graf pro Azure Cosmos DB
description: Aktualizace RU/s pro databázi Gremlin a graf pro Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 0ef01a50e4827ec2ba96ce5d370bc2386e0dbfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275518"
---
# <a name="update-rus-for-a-gremlin-database-and-graph-for-azure-cosmos-db-using-azure-cli"></a>Aktualizace RU/s pro databázi Gremlin a graf pro Azure Cosmos DB pomocí Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, toto téma vyžaduje, abyste spouštěli Azure CLI verze 2.0.73 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří databázi Gremlin se sdílenou propustností a Gremlin graf s vyhrazenou propustnost, pak aktualizuje propustnost pro databázi i graf.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Update RU/s for a Gremlin database and graph.")]

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
| [az cosmosdb gremlin databáze vytvořit](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | Vytvoří databázi Azure Cosmos Gremlin. |
| [az cosmosdb gremlin graf vytvořit](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Vytvoří graf Azure Cosmos Gremlin. |
| [Aktualizace propustnost databáze az cosmosdb gremlin](/cli/azure/cosmosdb/gremlin/database/throughput#az-cosmosdb-gremlin-database-throughput-update) | Aktualizace RU/s pro databázi Azure Cosmos Gremlin. |
| [aktualizace propustnost grafu az cosmosdb gremlin](/cli/azure/cosmosdb/gremlin/graph/throughput#az-cosmosdb-gremlin-graph-throughput-update) | Aktualizace RU/s pro graf Azure Cosmos Gremlin. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o cli DB Azure Cosmos najdete v [dokumentaci k příkazu KONT db Azure Cosmos](/cli/azure/cosmosdb)DB .

Všechny ukázky skriptu Rozhraní příkazového od Db BZI Azure Cosmos najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
