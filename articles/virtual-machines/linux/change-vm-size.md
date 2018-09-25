---
title: Jak změnit velikost virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Jak škálovat nahoru nebo dolů virtuálního počítače s Linuxem, změnou velikosti virtuálního počítače.
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
ms.openlocfilehash: 0b8802c91ceb59d391dc27a71da905de9c15a1dc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993221"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Změna velikosti virtuálního počítače s Linuxem pomocí Azure CLI 

Po zřízení virtuálního počítače (VM) virtuálního počítače můžete škálovat směrem nahoru nebo dolů, tak, že změníte [velikost virtuálního počítače][vm-sizes]. V některých případech je nejdřív uvolněte virtuální počítač. Je potřeba zrušit přidělení virtuálního počítače, pokud je požadovaná velikost není k dispozici v clusteru hardwaru, který je hostitelem virtuálního počítače. Tento článek podrobně popisuje, jak změnit velikost virtuálního počítače s Linuxem pomocí Azure CLI. 

## <a name="resize-a-vm"></a>Změna velikosti virtuálního počítače
Změna velikosti virtuálního počítače, budete potřebovat nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).

1. Zobrazit seznam dostupných velikostí virtuálních počítačů v clusteru hardwaru je hostitelem virtuálního počítače s [az vm seznam vm-resize-options](/cli/azure/vm#az_vm_list_vm_resize_options). Následující příklad zobrazí seznam velikostí virtuálních počítačů pro virtuální počítač s názvem `myVM` ve skupině prostředků `myResourceGroup` oblasti:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Pokud je uvedený požadovanou velikost virtuálního počítače, změna velikosti virtuálního počítače s [změnit velikost virtuálního počítače az](/cli/azure/vm#az_vm_resize). Následující příklad změní velikost virtuálního počítače s názvem `myVM` k `Standard_DS3_v2` velikost:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Virtuální počítač se restartuje během tohoto procesu. Po restartování budou přemapovány stávající operační systém a datové disky. Nic na dočasném disku se ztratí.

3. Pokud je požadovaná velikost virtuálního počítače není uvedený, budete muset nejprve zrušte přidělení virtuálního počítače s [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Tento proces umožňuje virtuálnímu počítači následně možnosti změnit velikost na libovolnou velikost, která je k dispozici, že oblasti podporuje a potom spuštěna. Následující kroky uvolnění, změna velikosti a potom spusťte virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Rušení přidělení virtuálního počítače uvolní také všechny dynamické IP adresy přiřazené k virtuálnímu počítači. Operační systém a datové disky nejsou ovlivněny.

## <a name="next-steps"></a>Další postup
Pro dodatečnou škálovatelnost spusťte několik instancí virtuálních počítačů a horizontální navýšení kapacity. Další informace najdete v tématu [automatické škálování počítačů s Linuxem ve Škálovací sadě virtuálních počítačů][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
