---
title: Jak změnit velikost virtuálního počítače s Linuxem pomocí azure CLI
description: Jak vertikálně navýšit nebo škálovat virtuální počítač s Linuxem změnou velikosti virtuálního počítače.
author: mikewasson
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: mwasson
ms.openlocfilehash: 20e7db80b55347c4a4a76b7c95d4d8bec368abda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969266"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Změna velikosti virtuálního počítače s Linuxem pomocí Azure CLI 

Po zřízení virtuálního počítače (VM), můžete škálovat virtuální počítač nahoru nebo dolů změnou [velikosti virtuálního počítače][vm-sizes]. V některých případech je nutné nejprve navrátit virtuální ho. Musíte navrátit virtuální počítač, pokud požadovaná velikost není k dispozici v hardwarovém clusteru, který je hostitelem virtuálního počítače. Tento článek podrobně popisuje, jak změnit velikost virtuálního počítače s Linuxem pomocí azure CLI. 

## <a name="resize-a-vm"></a>Změna velikosti virtuálního počítače
Chcete-li změnit velikost virtuálního počítače, budete potřebovat nejnovější [Azure CLI](/cli/azure/install-az-cli2) nainstalován a přihlášen k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

1. Zobrazení seznamu dostupných velikostí virtuálních zařízení v hardwarovém clusteru, kde je virtuální počítač hostovaný s [možnostmi změny velikosti az vm list-vm](/cli/azure/vm). V následujícím příkladu jsou uvedeny velikosti virtuálních počítače pro virtuální počítače pojmenované `myVM` v oblasti skupiny `myResourceGroup` prostředků:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Pokud je uvedena požadovaná velikost virtuálního počítače, změňte velikost virtuálního počítače s [velikostí az vm](/cli/azure/vm). Následující příklad změní velikost virtuálního `myVM` počítače `Standard_DS3_v2` pojmenované na velikost:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Virtuální počítač se restartuje během tohoto procesu. Po restartování jsou přemapovány stávající operační systém a datové disky. Cokoliv na dočasném disku je ztraceno.

3. Pokud požadovaná velikost virtuálního počítače není uvedena, musíte nejprve navrátit virtuální ho s [az vm navrátit](/cli/azure/vm). Tento proces umožňuje virtuálního počítače pak změnit velikost na libovolnou velikost, která je k dispozici, že oblast podporuje a pak se spustí. Následující kroky navrátit, změnit velikost a potom `myVM` spustit virtuální ho `myResourceGroup`nový virtuální počítače s názvem ve skupině prostředků s názvem :
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Zrušení přidělení virtuálního počítačů také uvolní všechny dynamické IP adresy přiřazené k virtuálnímu jemu. Operační systém a datové disky nejsou ovlivněny.

## <a name="next-steps"></a>Další kroky
Pro další škálovatelnost spusťte více instancí virtuálních počítačí a horizontální navýšení kapacity. Další informace najdete v tématu [Automatické škálování linuxových počítačů v sadě škálování virtuálních počítačů][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
