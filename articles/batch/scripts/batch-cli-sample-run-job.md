---
title: Ukázkový skript Azure CLI – spuštění úlohy služby Batch
description: Tento skript vytvoří úlohu Batch a přidá do ní řadu úkolů. Ukazuje také, jak monitorovat úlohu a její úkoly.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: b67925f48a9d2dbe0b4559d46d783b500e7a0773
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100911"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Příklad rozhraní příkazového řádku: Spuštění úlohy a úkolů pomocí služby Azure Batch

Tento skript vytvoří úlohu Batch a přidá do ní řadu úkolů. Ukazuje také, jak monitorovat úlohu a její úkoly. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.0.20 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Vytvoří účet Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Provede ověření v zadaném účtu Batch pro další práci s rozhraním příkazového řádku.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Vytvoří fond výpočetních uzlů.  |
| [az batch job create](/cli/azure/batch/job#az-batch-job-create) | Vytvoří úlohu Batch.  |
| [az batch task create](/cli/azure/batch/task#az-batch-task-create) | Přidá úkol do zadané úlohy Batch.  |
| [az batch job set](/cli/azure/batch/job#az-batch-job-set) | Aktualizuje vlastnosti úlohy Batch.  |
| [az batch job show](/cli/azure/batch/job#az-batch-job-show) | Načte podrobnosti o zadané úloze Batch.  |
| [az batch task show](/cli/azure/batch/task#az-batch-task-show) | Načte podrobnosti o úkolu ze zadané úlohy Batch.  |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
