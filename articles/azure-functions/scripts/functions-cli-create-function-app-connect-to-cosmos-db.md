---
title: Vytvoření funkce Azure, která se připojuje ke službě Azure Cosmos DB | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření funkce Azure, která se připojuje ke službě Azure Cosmos DB
services: functions
documentationcenter: functions
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/03/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 6001fc5737ee901ab1ab5af0dd2f913f05380ac2
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161403"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Vytvoření funkce Azure, která se připojuje ke službě Azure Cosmos DB

Tento ukázkový skript Azure Functions vytvoří aplikaci funkcí a připojí funkci k databázi Azure Cosmos DB. Nastavení vytvořené aplikace obsahující připojení můžete použít s [triggerem nebo vazbou služby Azure Cosmos DB](..\functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud používáte rozhraní příkazového řádku místně, ujistěte se, že máte Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

Tato ukázka vytvoří aplikaci funkcí Azure a přidá do nastavení aplikace koncový bod a přístupový klíč Azure Cosmos DB.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků s umístěním. |
| [az storage accounts create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | vytvořit účet úložiště |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Vytvoří aplikaci funkcí v [plánu Consumption](../functions-scale.md#consumption-plan) bez serveru. |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az-cosmosdb-create) | Vytvoří účet databáze Azure Cosmos DB. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).




