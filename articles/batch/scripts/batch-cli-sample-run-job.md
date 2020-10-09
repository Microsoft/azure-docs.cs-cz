---
title: Ukázkový skript Azure CLI – spuštění úlohy služby Batch
description: Tento skript vytvoří úlohu Batch a přidá do ní řadu úkolů. Ukazuje také, jak monitorovat úlohu a její úkoly.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: d8efce1bc8dc32070b770769104b7fdc38e683cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500227"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Příklad rozhraní příkazového řádku: Spuštění úlohy a úkolů pomocí služby Azure Batch

Tento skript vytvoří úlohu Batch a přidá do ní řadu úkolů. Ukazuje také, jak monitorovat úlohu a její úkoly. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

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
