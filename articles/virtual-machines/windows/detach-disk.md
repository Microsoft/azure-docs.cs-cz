---
title: Odpojení datového disku od virtuálního počítače s Windows – Azure
description: Odpojte datový disk z virtuálního počítače v Azure pomocí modelu nasazení Správce prostředků.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/08/2020
ms.author: cynthn
ms.openlocfilehash: f3c1e305024eb255a85aa470105d99edd3c26982
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201303"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Jak odpojit datový disk od virtuálního počítače s Windows

Když už nepotřebujete datový disk připojený k virtuálnímu počítači, můžete ho jednoduše odpojit. Tím se disk z virtuálního počítače odebere, ale neodebere se z úložiště.

> [!WARNING]
> Pokud disk odpojíte, nedojde k jeho automatickému odstranění. Pokud jste se přihlásili k odběru služby Premium Storage, bude se vám nadále účtovat poplatky za úložiště pro disk. Další informace najdete v tématu [ceny a fakturace při použití Premium Storage](../disks-types.md#billing).

Pokud znovu chcete použít stávající data na disku, můžete ho znovu připojit ke stejnému nebo jinému virtuálnímu počítači.

 

## <a name="detach-a-data-disk-using-powershell"></a>Odpojení datového disku pomocí PowerShellu

Pomocí PowerShellu můžete odstranit datový disk *za chodu* , ale zajistěte, aby tento disk aktivně nepoužívali předtím, než ho odpojíte od virtuálního počítače.

V tomto příkladu odebereme z virtuálního počítače **myVM** ve skupině prostředků **myResourceGroup** disk s názvem **myDisk** . Nejdřív disk odeberete pomocí rutiny [Remove-AzVMDataDisk](/powershell/module/az.compute/remove-azvmdatadisk) . Pak aktualizujete stav virtuálního počítače pomocí rutiny [Update-AzVM](/powershell/module/az.compute/update-azvm) , abyste mohli dokončit proces odebrání datového disku.

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

Disk zůstává v úložišti, ale už není připojený k virtuálnímu počítači.

## <a name="detach-a-data-disk-using-the-portal"></a>Odpojení datového disku pomocí portálu

Můžete odstranit *datový* disk, ale zajistěte, aby tento disk aktivně nepoužívali předtím, než ho odpojíte od virtuálního počítače.

1. V nabídce vlevo vyberte **Virtual Machines**.
1. Vyberte virtuální počítač s datovým diskem, který chcete odpojit.
1. V části **Nastavení** vyberte **Disky**.
1. V podokně **disky** v pravém dolním rohu datového disku, který chcete odpojit, klikněte na tlačítko **X** odstranit.
1. V horní části stránky vyberte **Uložit** a uložte provedené změny.

Disk zůstává v úložišti, ale už není připojený k virtuálnímu počítači.

## <a name="next-steps"></a>Další kroky

Pokud chcete znovu použít datový disk, můžete [ho jednoduše připojit k jinému virtuálnímu počítači](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .
