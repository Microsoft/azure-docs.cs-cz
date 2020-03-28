---
title: Aktualizace RU/s pro tabulku table api pro Azure Cosmos DB
description: Aktualizace RU/s pro tabulku table api pro Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 8cc04b766ba63fb522417310177a539ea04fcdd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275378"
---
# <a name="update-rus-for-a-table-api-table-for-azure-cosmos-db-azure-cli"></a>Aktualizace RU/s pro tabulku rozhraní API tabulky pro Azure Cosmos DB Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, toto téma vyžaduje, abyste spouštěli Azure CLI verze 2.0.73 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří tabulku rozhraní TABLE API a aktualizuje propustnost tabulky.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/throughput.sh "Update RU/s for a Table API table.")]

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
| [az cosmosdb tabulka vytvořit](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | Vytvoří tabulku rozhraní API tabulky Azure Cosmos. |
| [Aktualizace propustnost tabulky az cosmosdb](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-update) | Aktualizace RU/s pro tabulku rozhraní API tabulky Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o cli DB Azure Cosmos najdete v [dokumentaci k příkazu KONT db Azure Cosmos](/cli/azure/cosmosdb)DB .

Všechny ukázky skriptu Rozhraní příkazového od Db BZI Azure Cosmos najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
