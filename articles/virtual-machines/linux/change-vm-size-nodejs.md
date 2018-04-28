---
title: Jak změnit velikost virtuálního počítače s Linuxem pomocí Azure CLI 1.0 | Microsoft Docs
description: Jak škálovat nahoru i dolů virtuální počítač s Linuxem změnou velikosti virtuálního počítače.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4407a3662e6a50d650ab4ecc6dd1c2dad0c53bdb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="resize-a-linux-vm-with-azure-cli-10"></a>Změnit velikost virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure 1.0

## <a name="overview"></a>Přehled

Po zřízení virtuálního počítače (VM) virtuálního počítače můžete škálovat nahoru nebo dolů, můžete změnit [velikost virtuálního počítače][vm-sizes]. V některých případech se musí nejprve navrácení virtuálního počítače. To může dojít, pokud nová velikost není k dispozici na hardwaru clusteru, který je hostitelem virtuálního počítače.

Tento článek ukazuje, jak změnit velikost virtuálního počítače s Linuxem pomocí [rozhraní příkazového řádku Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#resize-a-linux-vm) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků


## <a name="resize-a-linux-vm"></a>Změnit velikost virtuálního počítače s Linuxem
Ke změně velikosti virtuálního počítače, proveďte následující kroky.

1. Spusťte následující příkaz rozhraní příkazového řádku. Tento příkaz vypíše velikosti virtuálních počítačů, které jsou k dispozici na hardwaru clusteru je hostitelem virtuálního počítače.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. Pokud požadovaná velikost je uveden, spusťte následující příkaz ke změně velikosti virtuálního počítače.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    Virtuální počítač se restartuje během tohoto procesu. Po restartování bude přemapování stávajícího operačního systému a datové disky. Nic na dočasné disku budou ztraceny.
   
    Použití `--enable-boot-diagnostics` možnost umožňuje [spouštění diagnostiky][boot-diagnostics], do protokolu chyby související se spuštění.
3. Jinak Pokud není uvedené požadované velikosti, spusťte následující příkazy se zrušit přidělení virtuálního počítače, jeho velikost a pak restartujte virtuální počítač.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > Rušení přidělení virtuálního počítače uvolní také všechny dynamické IP adresy přiřazené k virtuálnímu počítači. Ovlivněné nejsou disky operačního systému a data.
   > 
   > 

## <a name="next-steps"></a>Další postup
Pro další škálovatelnost spustit více instancí virtuálního počítače a horizontální rozšíření kapacity. Další informace najdete v tématu [automaticky škálovat počítače se systémem Linux v sadě škálování virtuálního počítače][scale-set]. 

<!-- links -->

[azure-cli]:../../cli-install-nodejs.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
