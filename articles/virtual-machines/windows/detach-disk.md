---
title: Odpojení datového disku od virtuálního počítače s Windows – Azure
description: Odpojte datový disk z virtuálního počítače v Azure pomocí modelu nasazení Správce prostředků.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 24c95d486ce77028a2c49917d8f98de23a3a8315
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552129"
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
1. V podokně **disky** v pravém dolním rohu datového disku, který chcete odpojit, vyberte tlačítko **X** , které chcete odpojit.
1. V horní části stránky vyberte **Uložit** a uložte provedené změny.

Disk zůstává v úložišti, ale už není připojený k virtuálnímu počítači. Disk není odstraněný.

## <a name="next-steps"></a>Další kroky

Pokud chcete znovu použít datový disk, můžete [ho jednoduše připojit k jinému virtuálnímu počítači](attach-managed-disk-portal.md).

Pokud chcete disk odstranit, takže už nebudete mít náklady na úložiště, přečtěte si téma [vyhledání a odstranění nepřipojeného spravovaného a nespravovaného disku Azure – Azure Portal](../disks-find-unattached-portal.md).
