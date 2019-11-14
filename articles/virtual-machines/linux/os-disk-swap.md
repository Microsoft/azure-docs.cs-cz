---
title: Výměna disku s operačním systémem pro virtuální počítač Azure pomocí rozhraní příkazového řádku
description: Změna disku operačního systému používaného virtuálním počítačem Azure pomocí rozhraní příkazového řádku
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035328"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Změna disku operačního systému používaného virtuálním počítačem Azure pomocí rozhraní příkazového řádku


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


Pro parametr `--osdisk` použijte [AZ VM Update](/cli/azure/vm#az-vm-update) s úplným ID prostředku nového disku. 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Restartujte virtuální počítač pomocí [AZ VM Start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Další kroky**

Pokud chcete vytvořit kopii disku, přečtěte si téma vytvoření [snímku disku](snapshot-copy-managed-disk.md).
