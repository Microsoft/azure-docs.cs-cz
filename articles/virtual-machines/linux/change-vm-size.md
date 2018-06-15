---
title: Jak změnit velikost virtuálního počítače s Linuxem pomocí Azure CLI 2.0 | Microsoft Docs
description: Jak škálovat nahoru i dolů virtuální počítač s Linuxem změnou velikosti virtuálního počítače.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5c396ad047567799b7541c1a125de0c1c51c39e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185990"
---
# <a name="resize-a-linux-virtual-machine-using-cli-20"></a>Změnit velikost virtuální počítač s Linuxem pomocí rozhraní příkazového řádku 2.0

Po zřízení virtuálního počítače (VM) virtuálního počítače můžete škálovat nahoru nebo dolů, můžete změnit [velikost virtuálního počítače][vm-sizes]. V některých případech se musí nejprve navrácení virtuálního počítače. Budete muset zrušit přidělení virtuálního počítače, pokud požadovaná velikost není k dispozici na hardwaru clusteru, který je hostitelem virtuálního počítače. Tento článek podrobné informace o tom, jak změnit velikost virtuálního počítače s Linuxem pomocí Azure CLI 2.0. K provedení těchto kroků můžete také využít [Azure CLI 1.0](change-vm-size-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="resize-a-vm"></a>Změna velikosti virtuálního počítače
Chcete-li změnit velikost virtuálního počítače, je třeba nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

1. Zobrazit seznam dostupných velikostí virtuálních počítačů v clusteru hardwaru je hostitelem virtuálního počítače s [az virtuálních počítačů seznamu-virtuálních počítačů-změny velikosti options](/cli/azure/vm#az_vm_list_vm_resize_options). Následující příklad vypíše velikosti virtuálních počítačů pro virtuální počítač s názvem `myVM` ve skupině prostředků `myResourceGroup` oblasti:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. V případě, že požadovaná velikost virtuálního počítače je uvedena, změňte velikost virtuálního počítače s [změnit velikost virtuálního počítače az](/cli/azure/vm#az_vm_resize). Následující příklad změní velikost virtuálního počítače s názvem `myVM` k `Standard_DS3_v2` velikost:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Virtuální počítač se restartuje během tohoto procesu. Po restartování jsou mapovány stávajícího operačního systému a datové disky. Nic na dočasné disku se ztratí.

3. Pokud není uvedené požadované velikosti virtuálního počítače, je třeba nejprve zrušit přidělení virtuálního počítače s [az OM deallocate](/cli/azure/vm#az_vm_deallocate). Tento proces umožňuje virtuálnímu počítači potom změnit na libovolnou velikost, která je k dispozici, že oblast podporuje a pak spustit. Následující kroky navrácení, přizpůsobit a pak spusťte virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Rušení přidělení virtuálního počítače uvolní také všechny dynamické IP adresy přiřazené k virtuálnímu počítači. Ovlivněné nejsou disky operačního systému a data.

## <a name="next-steps"></a>Další postup
Pro další škálovatelnost spustit více instancí virtuálního počítače a horizontální rozšíření kapacity. Další informace najdete v tématu [automaticky škálovat počítače se systémem Linux v sadě škálování virtuálního počítače][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
