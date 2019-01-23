---
title: Odpojení datového disku z virtuálního počítače s Windows – Azure | Dokumentace Microsoftu
description: Odpojení datového disku z virtuálního počítače v Azure s využitím modelu nasazení Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.component: disks
ms.openlocfilehash: 69db6f4e89720d2c0313628045e4ae0eda99013f
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465543"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Jak odpojit datový disk od virtuálního počítače s Windows

Když už nepotřebujete datový disk připojený k virtuálnímu počítači, můžete ho jednoduše odpojit. Odebrání disku z virtuálního počítače, ale neodebere se z úložiště.

> [!WARNING]
> Pokud odpojíte disk není automaticky odstraněn. Pokud odběru jste přihlášeni do Premium storage, budou dál účtovat poplatky za úložiště pro disk. Další informace najdete v tématu [ceny a fakturace při použití služby Premium Storage](premium-storage.md#pricing-and-billing).
>
>

Pokud znovu chcete použít stávající data na disku, můžete ho znovu připojit ke stejnému nebo jinému virtuálnímu počítači.


## <a name="detach-a-data-disk-using-powershell"></a>Odpojení datového disku pomocí Powershellu

Je možné *horké* odebrání datového disku pomocí prostředí PowerShell, ale ujistěte se, že nic aktivně používá disk před odpojením z virtuálního počítače.

V tomto příkladu jsme odebrat disk s názvem **myDisk** z virtuálního počítače **myVM** v **myResourceGroup** skupinu prostředků. Nejprve odebrat disk pomocí [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk) rutiny. Pak aktualizujte stav virtuálního počítače pomocí [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny dokončete proces odebrání datového disku.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

Disk zůstává v úložišti, ale už není připojen k virtuálnímu počítači.


## <a name="detach-a-data-disk-using-the-portal"></a>Odpojení datového disku pomocí portálu

1. V nabídce vlevo vyberte **virtuálních počítačů**.
2. Vyberte virtuální počítač, který má datový disk, který chcete odpojit a klikněte na tlačítko **Zastavit** k uvolnění virtuálního počítače.
3. V podokně virtuální počítač vyberte **disky**.
4. V horní části **disky** vyberte **upravit**.
5. V **disky** podokno na pravé straně datový disk, který chcete odpojit, klikněte na tlačítko ![obrázek tlačítka odpojení](./media/detach-disk/detach.png) odpojit tlačítko.
5. Po odebrání disku klikněte na tlačítko **Uložit** horní části podokna.
6. V podokně virtuální počítač, klikněte na tlačítko **přehled** a potom klikněte na tlačítko **Start** tlačítko v horní části podokna restartování virtuálního počítače.

Disk zůstává v úložišti, ale už není připojen k virtuálnímu počítači.

## <a name="next-steps"></a>Další postup
Pokud chcete znovu použít datový disk, jste právě [připojit k jinému virtuálnímu počítači](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

