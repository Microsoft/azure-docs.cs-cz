---
title: Převod virtuálního počítače s Windows z nespravovaných disků na managed disks – Azure Managed Disks | Dokumentace Microsoftu
description: Postup převedení virtuálního počítače s Windows z nespravovaných disků na spravované disky pomocí prostředí PowerShell v modelu nasazení Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: fecf17d95231cc37a141cfb72397f44ce2e980b5
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435596"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Převod virtuálního počítače s Windows z nespravovaných disků na managed disks

Pokud máte existující Windows virtuální počítače (VM), které používají nespravované disky virtuálních počítačů určených k použití spravovaných disků prostřednictvím můžete převést [Azure Managed Disks](managed-disks-overview.md) služby. Tento proces převede disk s operačním systémem i všechny připojené datové disky.

V tomto článku se dozvíte, jak převést virtuální počítače pomocí Azure Powershellu. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="before-you-begin"></a>Před zahájením


* Kontrola [naplánovat migraci do služby Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Kontrola [nejčastější dotazy týkající se migrace na spravované disky](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Převést virtuální počítače s jednou instancí
Tato část popisuje, jak převést virtuální počítače Azure s jednou instancí z nespravovaných disků na managed disks. (Pokud jsou vaše virtuální počítače ve skupině dostupnosti, najdete v další části.) 

1. Uvolněte virtuální počítač s použitím [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) rutiny. V následujícím příkladu se uvolní virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`: 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Převod virtuálního počítače na managed disks pomocí [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) rutiny. Následující proces převede předchozí virtuálního počítače, včetně disk s operačním systémem a všechny datové disky a spouští virtuální počítač:

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```



## <a name="convert-vms-in-an-availability-set"></a>Převod virtuálních počítačů ve skupině dostupnosti

Pokud virtuální počítače, které chcete převést na spravované disky jsou ve skupině dostupnosti, musíte nejprve převést dostupnosti na spravované skupině dostupnosti.

1. Převést skupině dostupnosti s využitím [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset) rutiny. Následující příklad aktualizuje skupinu dostupnosti `myAvailabilitySet` ve skupině prostředků s názvem `myResourceGroup`:

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Pokud se nachází v oblasti, kde vaší skupiny dostupnosti má pouze 2 domén selhání spravovaných, ale počet domén selhání nespravované je 3, tento příkaz zobrazí chybu podobný "počet domén selhání zadané 3 musí spadat do rozsahu 1 až 2." Chybu vyřešit, aktualizujte doméně selhání 2 a update `Sku` k `Aligned` následujícím způsobem:

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Zrušit přidělení a k převodu virtuálních počítačů ve skupině dostupnosti. Následující skript uvolní každý virtuální počítač s použitím [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) rutiny, převede ho pomocí [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk)a restartuje automaticky dvou procesu převodu :

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k chybě při převodu nebo pokud virtuální počítač je v chybovém stavu kvůli problémům v předchozí převodu, spusťte `ConvertTo-AzureRmVMManagedDisk` rutinu znovu. Jednoduché opakování obvykle odblokuje situace.
Před převodem, ujistěte se, že všechna rozšíření virtuálního počítače jsou ve stavu "Zřizování bylo úspěšné" nebo převod selže s kódem chyby 409.


## <a name="convert-using-the-azure-portal"></a>Převod pomocí webu Azure portal

Můžete také převést nespravovaných disků na managed disks, webu Azure portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte virtuální počítač ze seznamu virtuálních počítačů na portálu.
3. V okně pro virtuální počítač, vyberte **disky** z nabídky.
4. V horní části **disky** okně vyberte **migrace na spravované disky**.
5. Pokud se váš virtuální počítač je ve skupině dostupnosti, bude se upozornění na **migrace na spravované disky** okno, které je potřeba převést první skupinu dostupnosti. Upozornění by měl mít odkaz kliknete na převést skupinu dostupnosti. Jakmile se převést skupinu dostupnosti nebo pokud váš virtuální počítač není ve skupině dostupnosti, klikněte na **migrace** zahájíte proces migrace disků na managed disks. 

Virtuální počítač se zastaví a restartuje po dokončení migrace.

## <a name="next-steps"></a>Další postup

[Převést spravované disky úrovně standard na premium](convert-disk-storage.md)

Pořiďte si jen pro čtení virtuálního počítače pomocí [snímky](snapshot-copy-managed-disk.md).

