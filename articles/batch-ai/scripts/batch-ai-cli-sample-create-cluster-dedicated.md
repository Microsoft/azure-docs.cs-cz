---
title: Příklad skriptu Azure CLI – vytvoření vyhrazeného clusteru Batch AI | Microsoft Docs
description: Příklad skriptu Azure CLI – vytvoření a správa clusteru vyhrazených uzlů (virtuálních počítačů) Batch AI
services: batch-ai
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.devlang: azurecli
ms.topic: sample
ms.tgt-pltfrm: multiple
ms.workload: na
ms.date: 07/26/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 8ca2def6aa6ab463e5b2be0203791da671a41745
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697797"
---
# <a name="cli-example-create-and-manage-a-batch-ai-cluster-of-dedicated-nodes"></a>Příklad rozhraní příkazového řádku: Vytvářet a spravovat cluster Batch AI vyhrazené uzly

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

V tomto skriptu si ukážeme některé příkazy dostupné v Azure CLI, které umožňují vytvořit a spravovat cluster Batch AI složený z vyhrazených uzlů (virtuálních počítačů).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.38 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-dedicated.sh "Create Batch AI cluster - dedicated nodes")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujících příkazů odeberte skupiny prostředků, včetně všech přidružených prostředků.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup

# Remove resource group for the auto-storage account.
az group delete --name batchaiautostorage
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Vytvoří pracovní prostor služby Batch AI. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Vytvoří cluster Batch AI. |
| [az batchai cluster show](/cli/azure/batchai/cluster) | Zobrazí informace o clusteru Batch AI. |
| [az batchai cluster node list](/cli/azure/batchai/cluster/node) | Zobrazí seznam uzlů v clusteru Batch AI. |
| [az batchai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) | Změní velikost clusteru Batch AI.  |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).
