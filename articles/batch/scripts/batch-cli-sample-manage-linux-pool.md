---
title: Ukázkový skript Azure CLI – fond pro Linux ve službě Batch
description: Tento skript ukazuje některé příkazy, které jsou k dispozici v Azure CLI pro vytvoření a správu fondu výpočetních uzlů Linux v Azure Batch.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: b5e1bdccefffa7803fbe744e27c1b36ca719560d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768282"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>Příklad rozhraní příkazového řádku: Vytvoření a správa fondu s Linuxem ve službě Azure Batch

Tento skript ukazuje některé příkazy, které jsou k dispozici v Azure CLI pro vytvoření a správu fondu výpočetních uzlů Linux v Azure Batch.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.0.20 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

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
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Vypíše dostupné skladové položky agenta uzlu a informace o imagi.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Vytvoří fond výpočetních uzlů.  |
| [az batch pool resize](/cli/azure/batch/pool#az_batch_pool_resize) | Změní počet spuštěných virtuálních počítačů v zadaném fondu.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Zobrazí vlastnosti fondu.  |
| [az batch node list](/cli/azure/batch/node#az_batch_node_list) | Vypíše všechny výpočetní uzly v zadaném fondu.  |
| [az batch node reboot](/cli/azure/batch/node#az_batch_node_reboot) | Restartuje zadaný výpočetní uzel.  |
| [az batch node delete](/cli/azure/batch/node#az_batch_node_delete) | Odstraní uvedené uzly ze zadaného fondu.  |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
