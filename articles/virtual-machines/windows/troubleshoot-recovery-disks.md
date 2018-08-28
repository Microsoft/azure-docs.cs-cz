---
title: Použití Windows řešení potíží s virtuálních počítačů pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak řešit potíže virtuální počítač Windows v Azure s připojením disku s operačním systémem k obnovení virtuálního počítače pomocí Azure Powershellu
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: f099eefbc6d196f25c2b09669cdc1c3cdec68a12
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43050010"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Odstranění virtuálního počítače s Windows pomocí připojení disku s operačním systémem k obnovení virtuálního počítače pomocí Azure Powershellu
Pokud váš Windows virtuální počítač (VM) v Azure dojde k chybě spouštění nebo disku, budete muset provést postup řešení potíží na samotném disku. Běžným příkladem může být aktualizace selhání aplikace, která brání virtuálního počítače moci úspěšně spustil. Tento článek podrobně popisuje, jak pomocí Azure Powershellu pro připojení disku k jinému virtuálnímu počítači Windows opravte všechny chyby a pak opravte původního virtuálního počítače. 

> [!Important]
> Skripty v tomto článku se vztahují jenom na virtuální počítače, které používají [spravovaného disku](managed-disks-overview.md). 


## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Chcete-li změnit disk s operačním systémem virtuálního počítače jsme teď můžete použít Azure PowerShell. Už musíme odstranit a znovu vytvořte virtuální počítač.

Proces řešení potíží je následující:

1. Ovlivněné virtuální počítač zastavte.
2. Vytvoření snímku ze Disk s operačním systémem virtuálního počítače.
3. Vytvoření disku ze snímku disku operačního systému.
4. Připojte disk jako datový disk k obnovení virtuálního počítače.
5. Připojte se k obnovení virtuálního počítače. Upravení souborů nebo spuštění jakékoli nástroje a řešit potíže na zkopírovaný disk s operačním systémem.
6. Odpojte Image a odpojit disk od virtuálního počítače pro obnovení.
7. Změňte disk s operačním systémem pro ovlivněné virtuální počítač.

Skripty pro zotavení virtuálního počítače můžete použít k automatizaci kroky 1, 2, 3, 4, 6 a 7. Další dokumentaci a pokyny najdete v tématu [skripty pro zotavení virtuálního počítače pro virtuální počítač Resource Manageru](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager).

Ujistěte se, že máte [nejnovější Azure PowerShell](/powershell/azure/overview) nainstalovaný a přihlášení k vašemu předplatnému:

```powershell
Connect-AzureRmAccount
```

V následujících příkladech nahraďte názvy parametrů s vlastními hodnotami. 

## <a name="determine-boot-issues"></a>Určete spouštěcí problémy
Snímek obrazovky virtuálního počítače můžete zobrazit v Azure vám pomůže vyřešit problémy. Tento snímek obrazovky může pomoci určit, proč se virtuální počítač nepovede spustit. Následující příklad získá na snímku obrazovky z virtuálního počítače Windows s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Projděte si snímek obrazovky, chcete-li zjistit, proč tento virtuální počítač se nedaří spustit. Poznámka: všechny specifické chybové zprávy nebo kódy chyb, které jsou k dispozici.

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Následující příklad zastaví virtuální počítač s názvem `myVM` ze skupiny prostředků s názvem `myResourceGroup`:

```powershell
Stop-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Počkejte, dokud virtuální počítač má bylo dokončeno odstraňování před zpracováním k dalšímu kroku.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Vytvoření snímku ze Disk s operačním systémem virtuálního počítače

Následující příklad vytvoří snímek s názvem `mySnapshot` z operačního systému disku virtuálního počítače s názvem "myVM". 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azurermvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzureRmSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

Snímek je kopie virtuálního pevného disku úplné, jen pro čtení. Nemůže být připojen k virtuálnímu počítači. V dalším kroku vytvoříme disku z tohoto snímku.

## <a name="create-a-disk-from-the-snapshot"></a>Vytvoření disku ze snímku

Tento skript vytvoří spravovaný disk s názvem `newOSDisk` ze snímku s názvem `mysnapshot`.  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzureRmSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
$storageType = 'StandardLRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzureRmLocation
$location = 'eastus'

$snapshot = Get-AzureRmSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzureRmDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Teď máte kopii původní disk s operačním systémem. Připojte tento disk k jinému virtuálnímu počítači s Windows pro účely odstraňování potíží.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Připojte disk k jinému virtuálnímu počítači s Windows pro řešení potíží

Nyní můžeme připojit kopii původní disk s operačním systémem k virtuálnímu počítači jako datový disk. Tento proces umožňuje opravte chyby v konfiguraci, případně si můžete přečíst další aplikace nebo systému souborů protokolu na disku. Následující příklad připojí disk s názvem `newOSDisk` k virtuálnímu počítači s názvem `RecoveryVM`.

> [!NOTE]
> Připojit disk, kopii původní disk s operačním systémem a virtuální počítač pro obnovení musí být ve stejném umístění.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Připojení k virtuální počítač pro obnovení a oprava problémů na připojený disk

1. Připojení RDP k vaší obnovení virtuálního počítače pomocí příslušných přihlašovacích údajů. Následující příklad stáhne soubor připojení RDP pro virtuální počítač s názvem `RecoveryVM` ve skupině prostředků s názvem `myResourceGroup`a soubory ke stažení na `C:\Users\ops\Documents`"

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Datový disk by měl automaticky zjistil a připojené. Zobrazení seznamu připojených svazků určit písmeno jednotky následujícím způsobem:

    ```powershell
    Get-Disk
    ```

    Následující příklad výstupu ukazuje disk připojený disk **2**. (Můžete také použít `Get-Volume` zobrazíte písmeno jednotky):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Poté, co je připojena kopie původní disk s operačním systémem, můžete provést údržbu a řešení potíží s kroky, podle potřeby. Jakmile vyřešíte problémy, pokračujte následujícími kroky.

## <a name="unmount-and-detach-original-os-disk"></a>Odpojení původního disk s operačním systémem
Po vyřešení chyby odpojte Image a stávající disk se odpojit od váš virtuální počítač pro obnovení. Disk nelze použít s jakýkoli jiný virtuální počítač, dokud se neuvolní zapůjčení disk se připojuje k obnovení virtuálního počítače.

1. Z v rámci relace protokolu RDP, odpojte datový disk na váš virtuální počítač pro obnovení. Je třeba číslo disku z předchozího `Get-Disk` rutiny. Potom použijte `Set-Disk` nastavit jako v režimu offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Potvrzení na disku je nyní nastaven jako offline pomocí `Get-Disk` znovu. Následující příklad výstupu ukazuje, že disk je nyní nastaven jako offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Ukončete relaci RDP. Z relace prostředí Azure PowerShell, odeberte disk s názvem `newOSDisk` z virtuálního počítače s názvem "RecoveryVM".

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Změnit disk s operačním systémem pro ovlivněné virtuální počítač

Prostředí Azure PowerShell můžete použít pro prohození disků operačního systému. Není nutné odstranit a znovu vytvořte virtuální počítač.

Tento příklad zastaví virtuální počítač s názvem `myVM` a přiřadí disk s názvem `newOSDisk` jako nový disk s operačním systémem. 

```powershell
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Zkontrolujte a povolte diagnostiku spouštění

Následující příklad povolí diagnostické rozšíření na virtuální počítač s názvem `myVMDeployed` ve skupině prostředků s názvem `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k virtuálnímu počítači, přečtěte si téma [připojení řešení potíží s RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači, naleznete v tématu [řešit problémy s připojením aplikace na virtuálním počítači s Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Další informace o použití Resource Manageru najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
