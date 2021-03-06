---
title: Vytvoření aplikace Function App s připojeným úložištěm – Azure CLI
description: Ukázkový skript Azure CLI – Vytvoření funkce Azure, která se připojuje ke službě Azure Storage
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 539c3a7dd95045b2e569dbb339be0e5a0c845902
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786274"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Vytvoření aplikace funkcí s pojmenovaným připojením účtu úložiště 

Tento ukázkový skript Azure Functions vytvoří aplikaci funkcí a připojí funkci k účtu služby Azure Storage. Nastavení vytvořené aplikace obsahující připojení můžete použít s [triggerem nebo vazbou úložiště](../functions-bindings-storage-blob.md). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Tento kurz vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

Tato ukázka vytvoří aplikaci funkcí Azure a přidá do nastavení aplikace připojovací řetězec úložiště.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků s umístěním. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Vytvoření účtu úložiště |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Vytvoří aplikaci funkcí v [plánu spotřeby](../consumption-plan.md)bez serveru. |
| [AZ Storage Account show-Connection-String](/cli/azure/storage/account#az_storage_account_show_connection_string) | Získá připojovací řetězec pro účet. |
| [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | Nastaví připojovací řetězec jako nastavení aplikace ve Function App. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).
