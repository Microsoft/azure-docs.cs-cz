---
title: Disk odkládacího souboru operačního systému pro virtuální počítač Azure pomocí rozhraní příkazového řádku | Microsoft Docs
description: Změňte disk operačního systému používá virtuální počítač Azure pomocí rozhraní příkazového řádku.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 1732b60ee135b765cdeea43f981bcef9575ff32c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196207"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Změnit disk operačního systému používá virtuální počítač Azure pomocí rozhraní příkazového řádku


Pokud máte existující virtuální počítač, ale chcete Prohodit disku pro zálohy disku nebo jiný disk operačního systému, můžete použít rozhraní příkazového řádku Azure k výměně disket operačního systému. Nemáte odstranit a znovu vytvořte virtuální počítač. Spravovaných disků můžete použít i v jiné skupině prostředků, tak dlouho, dokud není již používán.

Virtuální počítač musí být stopped\deallocated a potom ID prostředku spravovaného disku lze nahradit s ID prostředku různých spravovaného disku. 

Ujistěte se, že typ velikosti a úložiště virtuálního počítače jsou kompatibilní se disk, který chcete připojit. Například pokud je disk, který chcete použít v Storage úrovně Premium, pak virtuální počítač musí být schopný Storage úrovně Premium (jako je velikost řady DS).

Tento článek vyžaduje Azure CLI verze 2.0.25 nebo vyšší. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 


Použití [seznam disků az](/cli/azure/disk#list) získáte seznam disků ve vaší skupině prostředků.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Použití [zastavení virtuálního počítače az](/cli/azure/vm#stop) k stop\deallocate virtuální počítač před odkládací disky.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Použití [aktualizace virtuálního počítače az](/cli/azure/vm#az-vm-update) s ID prostředku úplné nový disk pro `--osdisk` parametr 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Restartujte virtuální počítač pomocí [spuštění virtuálního počítače az](/cli/azure/vm#start).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Další kroky**

Vytvoření kopie disku naleznete v tématu [snímku disk](snapshot-copy-managed-disk.md).