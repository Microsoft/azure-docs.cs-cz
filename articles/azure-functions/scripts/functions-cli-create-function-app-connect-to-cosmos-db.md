---
title: "Vytvoření funkce Azure, která se připojuje ke službě Azure Cosmos DB | Microsoft Docs"
description: "Ukázkový skript Azure CLI – Vytvoření funkce Azure, která se připojuje ke službě Azure Cosmos DB"
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 01/22/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 092e0681b0491fc1c54c19e234aafdac6d428fd1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
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
| [az login](https://docs.microsoft.com/cli/azure/reference-index#az_login) | Přihlásí se k Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků s umístěním. |
| [az storage accounts create](https://docs.microsoft.com/cli/azure/storage/account) | vytvořit účet úložiště |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Vytvoří novou aplikaci funkcí. |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_create) | Vytvoří databázi Cosmos DB. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Vyčištění |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).




