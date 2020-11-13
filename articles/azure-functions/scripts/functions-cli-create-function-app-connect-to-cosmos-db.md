---
title: Vytvoření aplikace Function App pomocí Azure Cosmos DB – Azure CLI
description: Ukázkový skript Azure CLI – Vytvoření funkce Azure, která se připojuje ke službě Azure Cosmos DB
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 760017439ddc65ee929db1612b34b093d8bed8a0
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565394"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Vytvoření funkce Azure, která se připojuje ke službě Azure Cosmos DB

Tento ukázkový skript Azure Functions vytvoří aplikaci funkcí a připojí funkci k databázi Azure Cosmos DB. Nastavení vytvořené aplikace obsahující připojení můžete použít s [triggerem nebo vazbou služby Azure Cosmos DB](../functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Tento kurz vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. 

## <a name="sample-script"></a>Ukázkový skript

Tato ukázka vytvoří aplikaci funkcí Azure a přidá do nastavení aplikace koncový bod a přístupový klíč Azure Cosmos DB.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků s umístěním. |
| [az storage accounts create](/cli/azure/storage/account#az-storage-account-create) | vytvořit účet úložiště |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Vytvoří aplikaci funkcí v [plánu spotřeby](../functions-scale.md#consumption-plan)bez serveru. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Vytvoří účet databáze Azure Cosmos DB. |
| [AZ cosmosdb show](/cli/azure/cosmosdb#az-cosmosdb-show)| Získá připojení k databázovému účtu. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| Získá klíče pro databázi. |
| [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Nastaví připojovací řetězec jako nastavení aplikace ve Function App. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).




