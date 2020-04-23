---
title: Převod virtuálního počítače s Windows z nespravovaných disků na spravované disky
description: Převod virtuálního počítače systému Windows z nespravovaných disků na spravované disky pomocí prostředí PowerShell v modelu nasazení Správce prostředků
author: roygara
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: d8069b174b7a69cc2e6c47171159569c56a15563
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081944"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Převod virtuálního počítače s Windows z nespravovaných disků na spravované disky

Pokud máte existující virtuální počítače s Windows,, které používají nespravované disky, můžete virtuální počítače převést na spravované disky prostřednictvím [služby Spravované disky Azure.](managed-disks-overview.md) Tento proces převádí disk operačního systému i všechny připojené datové disky.

 

## <a name="before-you-begin"></a>Před zahájením


* Zkontrolujte [plán migrace na spravované disky](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Projděte si [nejčastější dotazy týkající se migrace na spravované disky](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Původní virtuální pevné disky a účet úložiště používané virtuálním počítačem před převodem se neodstraní. Budou se vám za ně i nadále účtovat poplatky. Abyste se vyhnuli účtování poplatků za tyto artefakty, po ověření dokončení převodu odstraňte původní objekty blob virtuálních pevných disků. Pokud potřebujete najít tyto nepřipojené disky, abyste je odstranili, přečtěte si náš článek [Vyhledání a odstranění nepřipojených disků Azure.](find-unattached-disks.md)


## <a name="convert-single-instance-vms"></a>Převod virtuálních ms s jednou instancí
Tato část popisuje, jak převést virtuální počítače Azure s jednou instancí z nespravovaných disků na spravované disky. (Pokud jsou vaše virtuální počítače v sadě dostupnosti, přečtěte si další část.) 

1. Zruší přidělení virtuálního virtuálního trhu pomocí rutiny [Stop-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) Následující příklad navrací virtuální hosti s `myVM` `myResourceGroup`názvem ve skupině prostředků s názvem : 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Převeďte virtuální ho na spravované disky pomocí rutiny [ConvertTo-AzVMManagedDisk.](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) Následující proces převede předchozí virtuální počítač, včetně disku operačního systému a všech datových disků, a spustí virtuální počítač:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Převod virtuálních aplikací v sadě dostupnosti

Pokud jsou virtuální počítače, které chcete převést na spravované disky, v sadě dostupnosti, musíte nejprve převést sadu dostupnosti na spravovanou skupinu dostupnosti.

1. Převeďte sadu dostupnosti pomocí rutiny [Update-AzAvailabilitySet.](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset) Následující příklad aktualizuje sadu `myAvailabilitySet` dostupnosti pojmenovanou ve skupině prostředků s názvem `myResourceGroup`:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Pokud oblast, kde je umístěna vaše skupina dostupnosti má pouze 2 spravované domény selhání, ale počet nespravovaných domén selhání je 3, tento příkaz zobrazí chybu podobnou "Zadaný počet chyb domény 3 musí spadat do rozsahu 1 až 2." Chcete-li chybu vyřešit, aktualizujte `Sku` doménu selhání na 2 a aktualizujte takto: `Aligned`

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Navrátit a převést virtuální chod v množině dostupnosti. Následující skript zruší přidělení každého virtuálního počítače pomocí rutiny [Stop-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) převede jej pomocí [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk)a automaticky jej restartuje jako druhý proces převodu:

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

Pokud dojde k chybě během převodu nebo pokud je virtuální měnový virtuální ms `ConvertTo-AzVMManagedDisk` a ve stavu selhání z důvodu problémů v předchozím převodu, spusťte rutinu znovu. Jednoduchý pokus obvykle odblokuje situaci.
Před převodem se ujistěte, že všechna rozšíření virtuálních virtuálních služeb jsou ve stavu "Zřizování proběhlo úspěšně", jinak se převod nezdaří s kódem chyby 409.

## <a name="convert-using-the-azure-portal"></a>Převod pomocí portálu Azure

Nespravované disky můžete také převést na spravované disky pomocí portálu Azure.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte virtuální ho virtuálního virtuálního motoru ze seznamu virtuálních discích na portálu.
3. V okně pro virtuální počítače vyberte **disky** z nabídky.
4. V horní části okna **Disky** vyberte **Migrovat na spravované disky**.
5. Pokud váš virtuální počítač je v sadě dostupnosti, bude upozornění na **migrate na spravované disky** okno, které je třeba převést sadu dostupnosti jako první. Upozornění by mělo mít odkaz, na který můžete kliknout a převést sadu dostupnosti. Jakmile se sada dostupnosti převede nebo pokud váš virtuální počítač není v sadě dostupnosti, klikněte na **Migrovat** a spusťte proces migrace disků na spravované disky.

Virtuální virtuální měsíč se po dokončení migrace zastaví a restartuje.

## <a name="next-steps"></a>Další kroky

[Převod standardních spravovaných disků na premium](convert-disk-storage.md)

Pořiďte kopii virtuálního počítače jen pro čtení pomocí [snímků](snapshot-copy-managed-disk.md).

