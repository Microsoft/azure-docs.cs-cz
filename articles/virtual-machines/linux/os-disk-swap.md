---
title: Disk odkládacího souboru operačního systému pro virtuální počítač Azure pomocí rozhraní příkazového řádku | Dokumentace Microsoftu
description: Disk s operačním systémem virtuálního počítače Azure pomocí rozhraní příkazového řádku používané změňte.
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
ms.openlocfilehash: 291bcec5a2a3d1ed2d49816b0ed7317bcfb07995
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54851852"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Změnit disk s operačním systémem používané Virtuálním počítači Azure pomocí rozhraní příkazového řádku


Pokud máte existující virtuální počítač, ale chcete Prohodit disku pro zálohování disku nebo jiný disk s operačním systémem, můžete využít rozhraní příkazového řádku Azure pro disky s operačním systémem. Není nutné odstranit a znovu vytvořte virtuální počítač. Tak dlouho, dokud se ještě nepoužívá, můžete použít i spravovaného disku v jiné skupině prostředků.

Virtuální počítač musí být stopped\deallocated a ID prostředku spravovaného disku se dá nahradit výrazem ID prostředku různých spravovaného disku. 

Ujistěte se, že typ velikosti a úložiště virtuálního počítače jsou kompatibilní s diskem, který chcete připojit. Například pokud ve službě Premium Storage se disk, který chcete použít, pak virtuální počítač musí být schopné Storage úrovně Premium (jako je velikost řady DS-series).

Tento článek vyžaduje použití Azure CLI verze 2.0.25 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 


Použití [az disk list](/cli/azure/disk) k získání seznamu disků ve vaší skupině prostředků.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Použití [az vm stop](/cli/azure/vm#stop) k stop\deallocate virtuální počítač před přechodem na discích.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Použití [az vm update](/cli/azure/vm#az-vm-update) s úplné ID prostředku nového disku `--osdisk` parametr 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Restartujte virtuální počítač pomocí [az vm start](/cli/azure/vm#start).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Další kroky**

Vytvoření kopie disku najdete v tématu [pořízení snímku disku](snapshot-copy-managed-disk.md).
