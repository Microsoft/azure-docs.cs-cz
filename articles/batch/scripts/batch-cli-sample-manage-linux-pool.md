---
title: "Ukázkový skript Azure CLI – Fond s Linuxem ve službě Batch | Microsoft Docs"
description: "Ukázkový skript Azure CLI – Vytvoření a správa fondu s Linuxem ve službě Batch"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 605db81a7f7c3d5c71992cc51d665b04aa1c589f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>Příklad rozhraní příkazového řádku: Vytvoření a správa fondu s Linuxem ve službě Azure Batch

Tento skript ukazuje několik příkazů dostupných v Azure CLI pro vytvoření a správu fondu výpočetních uzlů s Linuxem ve službě Azure Batch.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

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
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az_batch_pool_node_agent_skus_list) | Vypíše dostupné skladové položky agenta uzlu a informace o imagi.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Vytvoří fond výpočetních uzlů.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_resize) | Změní počet spuštěných virtuálních počítačů v zadaném fondu.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | Zobrazí vlastnosti fondu.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_list) | Vypíše všechny výpočetní uzly v zadaném fondu.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_reboot) | Restartuje zadaný výpočetní uzel.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_delete) | Odstraní uvedené uzly ze zadaného fondu.  |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).
