---
title: Vytvoření Function App v plánu App Service – Azure CLI
description: Ukázkový skript Azure CLI – Vytvoření aplikace funkcí v plánu služby App Service
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 020bd064628554703bb375c06c72e68d4536a2a3
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934437"
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Vytvoření aplikace funkcí v plánu služby App Service

Tento ukázkový skript Azure Functions vytvoří aplikaci funkcí, což je kontejner pro vaše funkce. Vytvářená aplikace funkcí využívá vyhrazený plán služby App Service, což znamená, že prostředky vašeho serveru jsou vždy zapnuté.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Tento kurz vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. 

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří aplikaci funkcí Azure s použitím vyhrazeného [plánu služby App Service](../dedicated-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Každý příkaz v tabulce odkazuje na příslušnou část dokumentace. Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet služby Azure Storage. |
| [AZ functionapp Plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | Vytvoří plán Premium. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Vytvoří v plánu služby App Service aplikaci funkcí. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).
