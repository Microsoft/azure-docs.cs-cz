---
title: Výměna disku operačního systému pro virtuální počítač Azure pomocí příkazového příkazového příkazu
description: Změňte disk operačního systému používaný virtuálním počítačem Azure pomocí příkazového příkazového příkazu.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: bfbe47fb68ffe7cee1ee2f9f7b94b418d8da2a1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035328"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Změna disku operačního systému používaného virtuálním počítačem Azure pomocí příkazového příkazového příkazu


Pokud máte existující virtuální počítač, ale chcete vyměnit disk za záložní disk nebo jiný disk operačního systému, můžete použít rozhraní příkazového řádku Azure k odhození disků operačního systému. Není nutné odstranit a znovu vytvořit virtuální ho. Spravovaný disk můžete dokonce použít v jiné skupině prostředků, pokud ještě není používán.

Virtuální modul je nutné zastavit\deallocated, pak ID prostředku spravovaného disku lze nahradit ID prostředku na jiném spravovaném disku. 

Ujistěte se, že velikost virtuálního počítače a typ úložiště jsou kompatibilní s diskem, který chcete připojit. Například pokud disk, který chcete použít, je v úložišti Premium, pak virtuální modul musí být schopný úložiště Premium (například velikost řady DS).

Tento článek vyžaduje Azure CLI verze 2.0.25 nebo vyšší. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 


Seznam [disků az](/cli/azure/disk) slouží k získání seznamu disků ve skupině prostředků.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Pomocí [zastavování virtuálního počítače az](/cli/azure/vm) vm zastavit\narozdělit virtuální ho před zamění disky.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Použití [aktualizace az vm](/cli/azure/vm#az-vm-update) s úplným ID `--osdisk` prostředku nového disku pro parametr 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Restartujte virtuální počítač pomocí [spuštění az vm](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Další kroky**

Chcete-li vytvořit kopii disku, přečtěte si [odkaz na snímek disku](snapshot-copy-managed-disk.md).
