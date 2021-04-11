---
title: Převod virtuálního počítače s Windows z nespravovaných disků na spravované disky
description: Jak převést virtuální počítač s Windows z nespravovaných disků na Managed disks pomocí prostředí PowerShell v modelu nasazení Správce prostředků
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 3f586c8907ded618b4cc4aaaadf3c87471cda0b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550718"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Převod virtuálního počítače s Windows z nespravovaných disků na spravované disky

Pokud máte existující virtuální počítače s Windows, které používají nespravované disky, můžete virtuální počítače převést tak, aby používaly spravované disky prostřednictvím služby [Azure Managed disks](../managed-disks-overview.md) . Tento proces převede disk s operačním systémem i všechny připojené datové disky.

 

## <a name="before-you-begin"></a>Než začnete


* Přečtěte si [plán migrace na Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Přečtěte si [Nejčastější dotazy týkající se migrace na Managed disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Původní virtuální pevné disky a účet úložiště používané virtuálním počítačem před převodem se neodstraní. Budou se vám za ně i nadále účtovat poplatky. Abyste se vyhnuli účtování poplatků za tyto artefakty, po ověření dokončení převodu odstraňte původní objekty blob virtuálních pevných disků. Pokud potřebujete najít tyto nepřipojené disky a odstranit je, přečtěte si náš článek [hledání a odstraňování nepřipojených spravovaných a nespravovaných disků Azure](find-unattached-disks.md).


## <a name="convert-single-instance-vms"></a>Převod virtuálních počítačů s jednou instancí
Tato část popisuje, jak převést virtuální počítače Azure s jednou instancí z nespravovaných disků na Managed disks. (Pokud jsou vaše virtuální počítače ve skupině dostupnosti, přečtěte si další část.) 

1. Zrušte přidělení virtuálního počítače pomocí rutiny [stop-AzVM](/powershell/module/az.compute/stop-azvm) . Následující příklad zruší přidělení virtuálního počítače `myVM` ve skupině prostředků s názvem `myResourceGroup` : 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Pomocí rutiny [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk) PŘEVEĎTE virtuální počítač na spravované disky. Následující postup převede předchozí virtuální počítač, včetně disku s operačním systémem a všech datových disků, a spustí virtuální počítač:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Převod virtuálních počítačů ve skupině dostupnosti

Pokud jsou virtuální počítače, které chcete převést na spravované disky, ve skupině dostupnosti, musíte nejdřív převést skupinu dostupnosti na spravovanou skupinu dostupnosti.

1. Pomocí rutiny [Update-AzAvailabilitySet](/powershell/module/az.compute/update-azavailabilityset) převeďte skupinu dostupnosti. Následující příklad aktualizuje skupinu dostupnosti s názvem `myAvailabilitySet` ve skupině prostředků s názvem `myResourceGroup` :

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Pokud se v oblasti, kde je umístěna vaše skupina dostupnosti, nachází pouze 2 spravované domény selhání, ale počet nespravovaných domén selhání je 3, zobrazí tento příkaz chybu podobný řetězci "zadaný počet domén selhání 3 musí klesnout do rozsahu 1 až 2." Pokud chcete chybu vyřešit, aktualizujte doménu selhání na 2 a aktualizujte ji následujícím způsobem `Sku` `Aligned` :

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Navrácení a převod virtuálních počítačů ve skupině dostupnosti. Následující skript zruší přidělení každého virtuálního počítače pomocí rutiny [stop-AzVM](/powershell/module/az.compute/stop-azvm) , převede ho pomocí [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk)a automaticky ho restartuje, i když je kromě procesu převodu:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Řešení potíží

Pokud při převodu dojde k chybě nebo pokud je virtuální počítač ve stavu selhání kvůli problémům s předchozím převodem, spusťte `ConvertTo-AzVMManagedDisk` rutinu znovu. Jednoduchý opakování obvykle odblokuje situaci.
Před převodem se ujistěte, že jsou všechna rozšíření virtuálních počítačů ve stavu "zřizování proběhlo úspěšně", nebo převod selže s kódem chyby 409.

## <a name="convert-using-the-azure-portal"></a>Převést pomocí Azure Portal

Nespravované disky můžete také převést na spravované disky pomocí Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte virtuální počítač ze seznamu virtuálních počítačů na portálu.
3. V okně pro virtuální počítač vyberte z nabídky **disky** .
4. V horní části okna **disky** vyberte **migrovat na Managed disks**.
5. Pokud je váš virtuální počítač ve skupině dostupnosti, v okně **migrovat do spravovaných disků** se zobrazí upozornění, že nejdřív potřebujete skupinu dostupnosti převést. Upozornění by mělo mít odkaz, který můžete kliknutím převést na skupinu dostupnosti. Po převedení skupiny dostupnosti nebo pokud váš virtuální počítač není ve skupině dostupnosti, klikněte na **migrovat** a zahajte proces migrace disků na spravované disky.

Po dokončení migrace se virtuální počítač zastaví a restartuje.

## <a name="next-steps"></a>Další kroky

[Převod standardních spravovaných disků na úroveň Premium](convert-disk-storage.md)

Poznamenejte si kopii virtuálního počítače jen pro čtení pomocí [snímků](snapshot-copy-managed-disk.md).