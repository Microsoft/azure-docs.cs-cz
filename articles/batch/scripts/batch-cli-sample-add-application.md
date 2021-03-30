---
title: Ukázkový skript Azure CLI – Přidání aplikace ve službě Batch
description: Tento ukázkový skript ukazuje, jak přidat aplikaci pro použití s fondem Azure Batch nebo úlohou.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 414a401168b1b378ed33f4904607243de0267d22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94565802"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Příklad rozhraní příkazového řádku: Přidání aplikace do účtu Azure Batch

Tento skript ukazuje, jak přidat aplikaci pro použití s fondem nebo úlohou Azure Batch. Aplikaci nastavíte pro přidání do účtu Batch tak, že zabalíte spustitelný soubor spolu se všemi závislostmi do souboru zip. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.0.20 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy.
Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Vytvoří účet Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Provede ověření v zadaném účtu Batch pro další práci s rozhraním příkazového řádku.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Vytvoří aplikaci.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Přidá balíček aplikace do zadané aplikace.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Aktualizuje vlastnosti aplikace.  |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
