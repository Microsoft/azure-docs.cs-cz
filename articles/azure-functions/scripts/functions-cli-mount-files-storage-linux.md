---
title: Připojení sdílené složky do aplikace pro funkce Pythonu – Azure CLI
description: Vytvořte aplikaci funkcí Pythonu bez serveru a připojte existující sdílenou složku pomocí azure cli.
ms.topic: sample
ms.date: 03/01/2020
ms.openlocfilehash: 3d66f84d124b36f1be335c3a2204f21690510ee8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79086457"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Připojení sdílené složky do aplikace pro funkce Pythonu pomocí azure cli

Tento ukázkový skript Azure Functions vytvoří aplikaci funkcí a vytvoří sdílenou složku v Azure Files. Připojí sdílenou složku tak, aby k datům přistupovaly vaše funkce.  

>[!NOTE]
>Vytvořená aplikace funkce běží na Pythonu verze 3.7. Funkce Azure také [podporuje Python verze 3.6 a 3.8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). Příklady jsou napsány pro prostředí Bash a musí být upraveny tak, aby byly spuštěny v příkazovém řádku systému Windows. 

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří aplikaci Azure Function pomocí [plánu Spotřeba](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Každý příkaz v tabulce odkazuje na příslušnou část dokumentace. Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet služby Azure Storage. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Vytvoří aplikaci funkcí. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Vytvoří sdílené složky Azure Files v účtu úložiště. | 
| [az úložiště adresář vytvořit](/cli/azure/storage/directory#az-storage-directory-create) | Vytvoří adresář ve sdílené složce. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Připojí sdílenou složku do aplikace funkce. |
| [az webapp config seznam účtů úložiště](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Zobrazuje sdílené složky připojené k aplikaci funkce. | 

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).
