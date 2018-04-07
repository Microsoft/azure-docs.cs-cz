---
title: Odpojit datový disk z virtuálního počítače Windows - Azure | Microsoft Docs
description: Naučte se odpojit datový disk z virtuálního počítače v Azure pomocí modelu nasazení Resource Manager.
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
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: e56e9ce22cc9e2bad75c944c20bff812d8720d18
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Jak se odpojit datový disk od virtuálního počítače s Windows
Když už nepotřebujete datový disk připojený k virtuálnímu počítači, můžete ho jednoduše odpojit. Odebere disk z virtuálního počítače, ale neodstraní z úložiště.

> [!WARNING]
> Pokud se odpojit disk není automaticky odstraněn. Pokud jste přihlášení k odběru služby storage úrovně Premium, můžete nadále toho vám být účtovány poplatky za úložiště pro disk. Další informace najdete v části [ceny a fakturace při použití služby Premium Storage](premium-storage.md#pricing-and-billing).
>
>

Pokud znovu chcete použít stávající data na disku, můžete ho znovu připojit ke stejnému nebo jinému virtuálnímu počítači.

## <a name="detach-a-data-disk-using-the-portal"></a>Odpojení datového disku pomocí portálu

1. V nabídce vlevo vyberte **virtuální počítače**.
2. Vyberte virtuální počítač, který má datový disk, kterou chcete odpojit a klikněte na tlačítko **Zastavit** se zrušit přidělení virtuálního počítače.
3. V podokně virtuální počítač vyberte **disky**.
4. V horní části **disky** podokně, vyberte **upravit**.
5. V **disky** podokně na pravé straně datový disk, který chcete odpojit, klikněte na tlačítko ![obrázek tlačítka odpojení](./media/detach-disk/detach.png) odpojit tlačítko.
5. Po odebrání disku, klikněte na tlačítko **Uložit** nahoře v podokně.
6. V podokně virtuální počítač, klikněte na tlačítko **přehled** a pak klikněte na tlačítko **spustit** tlačítka v horní části podokna restartování virtuálního počítače.



Disk zůstává v úložišti, ale už není připojený k virtuálnímu počítači.

## <a name="detach-a-data-disk-using-powershell"></a>Odpojit datový disk pomocí prostředí PowerShell
V tomto příkladu v prvním příkazu je získán virtuální počítač s názvem **MyVM07** v **RG11** pomocí skupiny prostředků [Get-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny a ukládá je **$VirtualMachine** proměnné.

Druhý řádek odebere datový disk s názvem DataDisk3 z virtuálního počítače pomocí [odebrat AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk) rutiny.

Ve třetím řádku aktualizuje stav virtuálního počítače pomocí [aktualizace-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny, dokončete proces odebrání datový disk.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -VM $VirtualMachine
```

Další informace najdete v tématu [odebrat AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Další postup
Pokud chcete použít datový disk, jste právě [připojte ji k jiným virtuálním Počítačem](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

