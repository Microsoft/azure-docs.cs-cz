---
title: Prohození mezi disky s operačním systémem pomocí Azure CLI
description: Změna disku operačního systému používaného virtuálním počítačem Azure pomocí rozhraní příkazového řádku Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 85d6350a36e62ace8f1922d30493d0f1d448d315
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765930"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-azure-cli"></a>Změna disku s operačním systémem používaného virtuálním počítačem Azure pomocí Azure CLI


Pokud máte existující virtuální počítač, ale chcete disk zaměnit na záložní disk nebo jiný disk s operačním systémem, můžete použít rozhraní příkazového řádku Azure pro prohození disků s operačním systémem. Nemusíte odstranit a znovu vytvořit virtuální počítač. Můžete dokonce použít spravovaný disk v jiné skupině prostředků, pokud se ještě nepoužívá.

Virtuální počítač musí být stopped\deallocated, a ID prostředku spravovaného disku se dá nahradit ID prostředku jiného spravovaného disku. 

Ujistěte se, že velikost virtuálního počítače a typ úložiště jsou kompatibilní s diskem, který chcete připojit. Pokud je například disk, který chcete použít, v Premium Storage, musí být virtuální počítač schopný Premium Storage (například velikost řady DS-Series).

Tento článek vyžaduje Azure CLI verze 2.0.25 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 


Pomocí [AZ disk list](/cli/azure/disk) získáte seznam disků ve vaší skupině prostředků.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Pomocí [AZ VM stop](/cli/azure/vm) stop\deallocate virtuální počítač a teprve potom disky odkládací.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Použijte [AZ VM Update](/cli/azure/vm#az_vm_update) s úplným ID prostředku nového disku pro `--osdisk` parametr. 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Restartujte virtuální počítač pomocí [AZ VM Start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Další kroky**

Pokud chcete vytvořit kopii disku, přečtěte si téma vytvoření [snímku disku](snapshot-copy-managed-disk.md).
