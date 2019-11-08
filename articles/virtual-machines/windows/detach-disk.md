---
title: Odpojení datového disku od virtuálního počítače s Windows – Azure | Microsoft Docs
description: Odpojte datový disk z virtuálního počítače v Azure pomocí modelu nasazení Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: de33ed80055c26870a29b4c4393a9072ec8dda5e
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749486"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Odpojení datového disku od virtuálního počítače s Windows

Když už nepotřebujete datový disk připojený k virtuálnímu počítači, můžete ho jednoduše odpojit. Tím se disk z virtuálního počítače odebere, ale neodebere se z úložiště.

> [!WARNING]
> Pokud disk odpojíte, nedojde k jeho automatickému odstranění. Pokud jste se přihlásili k odběru služby Premium Storage, bude se vám nadále účtovat poplatky za úložiště pro disk. Další informace najdete v tématu [ceny a fakturace při použití Premium Storage](disks-types.md#billing).

Pokud znovu chcete použít stávající data na disku, můžete ho znovu připojit ke stejnému nebo jinému virtuálnímu počítači.

 

## <a name="detach-a-data-disk-using-powershell"></a>Odpojení datového disku pomocí PowerShellu

Pomocí PowerShellu můžete odstranit datový disk *za chodu* , ale zajistěte, aby tento disk aktivně nepoužívali předtím, než ho odpojíte od virtuálního počítače.

V tomto příkladu odebereme z virtuálního počítače **myVM** ve skupině prostředků **myResourceGroup** disk s názvem **myDisk** . Nejdřív disk odeberete pomocí rutiny [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) . Pak aktualizujete stav virtuálního počítače pomocí rutiny [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) , abyste mohli dokončit proces odebrání datového disku.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

Disk zůstává v úložišti, ale už není připojený k virtuálnímu počítači.

## <a name="detach-a-data-disk-using-the-portal"></a>Odpojení datového disku pomocí portálu

1. V nabídce vlevo vyberte **Virtual Machines**.
2. Vyberte virtuální počítač s datovým diskem, který chcete odpojit, a kliknutím na **zastavit** zrušte přidělení virtuálního počítače.
3. V podokně virtuální počítač vyberte **disky**.
4. V horní části podokna **disky** vyberte **Upravit**.
5. V podokně **disky** v pravém dolním rohu datového disku, který chcete odpojit, klikněte na obrázek tlačítka pro odpojení ![](./media/detach-disk/detach.png) tlačítko Odpojit.
5. Po odebrání disku klikněte na **Uložit** v horní části podokna.
6. V podokně virtuální počítač klikněte na **Přehled** a potom kliknutím na tlačítko **Start** v horní části podokna restartujte virtuální počítač.

Disk zůstává v úložišti, ale už není připojený k virtuálnímu počítači.

## <a name="next-steps"></a>Další kroky

Pokud chcete znovu použít datový disk, můžete [ho jednoduše připojit k jinému virtuálnímu počítači](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .