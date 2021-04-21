---
title: Připojení sdílené složky k aplikaci funkcí Python – Azure CLI
description: Vytvořte aplikaci funkcí Python bez serveru a připojte existující sdílenou složku pomocí Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d0037cea24b1989c4f7a4d2ddd6bf3f8f7e812b3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762276"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Připojení sdílené složky k aplikaci funkcí Pythonu pomocí rozhraní příkazového řádku Azure

Tento Azure Functions ukázkový skript vytvoří aplikaci funkcí a vytvoří sdílenou složku ve službě soubory Azure. Připojí sdílenou složku, aby k nim bylo možné získávat data prostřednictvím vašich funkcí.  

>[!NOTE]
>Vytvořená aplikace Function App běží v Pythonu verze 3,7. Azure Functions [podporuje také Python verze 3,6 a 3,8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Tento kurz vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. 

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří aplikaci funkcí v Azure Functions pomocí [plánu spotřeby](../consumption-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Každý příkaz v tabulce odkazuje na příslušnou část dokumentace. Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Vytvoří účet služby Azure Storage. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Vytvoří aplikaci funkcí. |
| [az storage share create](/cli/azure/storage/share#az_storage_share_create) | Vytvoří sdílenou složku služby soubory Azure v účtu úložiště. | 
| [AZ Storage Directory Create](/cli/azure/storage/directory#az_storage_directory_create) | Vytvoří adresář ve sdílené složce. |
| [AZ WebApp config Storage-Account Add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) | Připojí sdílenou složku do aplikace Function App. |
| [AZ WebApp config Storage-Account list](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_list) | Zobrazuje sdílené složky připojené k aplikaci Function App. | 

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).
