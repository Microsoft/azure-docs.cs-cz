---
title: Ukázkový skript Azure CLI – Fond s Linuxem ve službě Batch | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření a správa fondu s Linuxem ve službě Batch
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: e365987878190b9f8a5ad1fd0f10015544b85947
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975543"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>Příklad rozhraní příkazového řádku: Vytvoření a správa fondu s Linuxem ve službě Azure Batch

Tento skript ukazuje několik příkazů dostupných v Azure CLI pro vytvoření a správu fondu výpočetních uzlů s Linuxem ve službě Azure Batch.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

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
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Vytvoří účet Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Provede ověření v zadaném účtu Batch pro další práci s rozhraním příkazového řádku.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az-batch-pool-node-agent-skus-list) | Vypíše dostupné skladové položky agenta uzlu a informace o imagi.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-create) | Vytvoří fond výpočetních uzlů.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-resize) | Změní počet spuštěných virtuálních počítačů v zadaném fondu.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-show) | Zobrazí vlastnosti fondu.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-list) | Vypíše všechny výpočetní uzly v zadaném fondu.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-reboot) | Restartuje zadaný výpočetní uzel.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-delete) | Odstraní uvedené uzly ze zadaného fondu.  |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).
