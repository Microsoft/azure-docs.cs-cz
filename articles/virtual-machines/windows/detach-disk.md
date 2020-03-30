---
title: Odpojení datového disku od virtuálního počítače s Windows – Azure
description: Odpojte datový disk od virtuálního počítače v Azure pomocí modelu nasazení Správce prostředků.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/08/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 301f3abd26f702f3f29c8833c835ba7d0e41bcaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834609"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Jak odpojit datový disk od virtuálního počítače s Windows

Když už nepotřebujete datový disk připojený k virtuálnímu počítači, můžete ho jednoduše odpojit. Tím odeberete disk z virtuálního počítače, ale neodeberete ho z úložiště.

> [!WARNING]
> Pokud disk odpojíte, nebude automaticky odstraněn. Pokud jste se přihlásili k odběru úložiště Premium, budou vám na dále účtovány poplatky za úložiště na disku. Další informace najdete [v tématu Ceny a fakturace při použití úložiště Premium](disks-types.md#billing).

Pokud znovu chcete použít stávající data na disku, můžete ho znovu připojit ke stejnému nebo jinému virtuálnímu počítači.

 

## <a name="detach-a-data-disk-using-powershell"></a>Odpojení datového disku pomocí prostředí PowerShell

Datový disk *můžete* odebrat pomocí prostředí PowerShell, ale ujistěte se, že disk není aktivně používán, než ho odpojíte od virtuálního počítače.

V tomto příkladu odebereme disk s názvem **myDisk** z **myVM** virtuálního počítače ve skupině prostředků **myResourceGroup.** Nejprve odeberete disk pomocí rutiny [Remove-AzVMDataDisk.](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) Potom aktualizovat stav virtuálního počítače, pomocí [rutiny Update-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) k dokončení procesu odebrání datového disku.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

Disk zůstane v úložišti, ale už není připojený k virtuálnímu počítači.

## <a name="detach-a-data-disk-using-the-portal"></a>Odpojení datového disku pomocí portálu

Datový disk můžete *odebrat horkou,* ale před odpojením od virtuálního počítače se ujistěte, že disk aktivně nepoužíváte.

1. V levé nabídce vyberte **Virtuální počítače**.
1. Vyberte virtuální počítač s datovým diskem, který chcete odpojit.
1. V části **Nastavení** vyberte **Disky**.
1. V horní části podokna **Disky** vyberte **Upravit**.
1. V podokně **Disky** zcela vpravo od datového disku, který chcete odpojit, vyberte **odpojit**.
1. V horní části stránky vyberte **Uložit,** chcete-li změny uložit.

Disk zůstane v úložišti, ale už není připojený k virtuálnímu počítači.

## <a name="next-steps"></a>Další kroky

Pokud chcete datový disk znovu použít, stačí [ho připojit k jinému virtuálnímu virtuálnímu počítače.](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
