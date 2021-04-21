---
title: Ukázkový skript Azure CLI – spuštění úlohy služby Batch
description: Tento skript vytvoří úlohu Batch a přidá do ní řadu úkolů. Ukazuje také, jak monitorovat úlohu a její úkoly.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e51fa96370b46c9a5a5d5ed6fd7593a06e45430
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768156"
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
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Vytvoří účet Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Provede ověření v zadaném účtu Batch pro další práci s rozhraním příkazového řádku.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Vytvoří fond výpočetních uzlů.  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | Vytvoří úlohu Batch.  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | Přidá úkol do zadané úlohy Batch.  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | Aktualizuje vlastnosti úlohy Batch.  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | Načte podrobnosti o zadané úloze Batch.  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | Načte podrobnosti o úkolu ze zadané úlohy Batch.  |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
