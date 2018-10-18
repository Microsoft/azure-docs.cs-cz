---
title: Příklad skriptu Azure CLI – vytvoření clusteru Batch AI pomocí konfiguračního souboru | Microsoft Docs
description: Příklad skriptu Azure CLI – vytvoření clusteru Batch AI zadáním konfiguračních nastavení do souboru JSON
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
ms.date: 08/16/2018
ms.author: danlep
ms.openlocfilehash: a1e472d237977d1948c69828d8ec391522896774
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44058114"
---
# <a name="cli-example-create-a-batch-ai-cluster-using-a-cluster-configuration-file"></a>Příklad rozhraní příkazového řádku: Vytvoření clusteru Batch AI pomocí konfiguračního souboru clusteru

Tento skript ukazuje, jak použít konfigurační soubor JSON k zadání nastavení clusteru Batch AI. Tato nastavení použijte místo odpovídajících parametrů příkazového řádku pro `az batchai cluster create`. Konfigurační soubor je užitečný, když potřebujete k uzlům clusteru připojit více systémů souborů nebo chcete použít stejnou konfiguraci v několika clusterech.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.38 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-config-file.sh "Create Batch AI cluster - configuration file")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujících příkazů odeberte skupiny prostředků a všechny k nim přidružené prostředky.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Vytvoří sdílenou složku v účtu úložiště. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Vytvoří pracovní prostor služby Batch AI. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Vytvoří cluster Batch AI. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Zobrazí informace o clusteru Batch AI. |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).
