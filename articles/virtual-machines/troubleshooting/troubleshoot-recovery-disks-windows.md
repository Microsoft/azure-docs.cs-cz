---
title: Použití virtuálního počítače pro řešení potíží s Windows s Azure PowerShellem | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit problémy s virtuálními počítači s Windows v Azure připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí Azure PowerShellu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 66cda98f272e7353b620059a731972714db585ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374128"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Poradce při potížích s virtuálním počítačem s Windows připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí Azure PowerShellu
Pokud váš virtuální počítač (VM) windows v Azure narazí na chybu při spuštění nebo disku, budete muset provést kroky řešení potíží na samotném disku. Běžným příkladem by mohla být neúspěšná aktualizace aplikace, která brání úspěšnému spuštění virtuálního virtuálního movitého virtuálního movitého virtuálního softwaru. Tento článek podrobně popisuje, jak pomocí Azure PowerShellu připojit disk k jinému virtuálnímu počítači windows opravit případné chyby a pak opravit původní virtuální počítač. 

> [!Important]
> Skripty v tomto článku platí jenom pro virtuální počítače, které používají [spravovaný disk](../windows/managed-disks-overview.md). 

 

## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Teď můžeme použít Azure PowerShell ke změně disku operačního systému pro virtuální počítač. Už nepotřebujeme odstranit a znovu vytvořit virtuální ho.

Proces řešení potíží je následující:

1. Zastavte ovlivněný virtuální virtuální ms.
2. Vytvořte snímek z disku operačního systému virtuálního počítače.
3. Vytvořte disk ze snímku disku operačního systému.
4. Připojte disk jako datový disk k virtuálnímu virtuálnímu počítače pro obnovení.
5. Připojte se k virtuálnímu virtuálnímu virtuálnímu zařízení pro obnovení. Upravte soubory nebo spusťte libovolné nástroje k opravě problémů na zkopírovaném disku operačního systému.
6. Odpojte a odpojte disk od virtuálního počítače pro obnovení.
7. Změňte disk operačního systému pro ovlivněný virtuální modul.

Příkazy pro opravu virtuálních mís můžete použít k automatizaci kroků 1, 2, 3, 4, 6 a 7. Další dokumentaci a pokyny najdete [v tématu Oprava virtuálního počítače s Windows pomocí příkazů pro opravu virtuálního počítače Azure](repair-windows-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ujistěte se, že máte nainstalovaný a přihlášený [nejnovější Azure PowerShell:](/powershell/azure/overview)

```powershell
Connect-AzAccount
```

V následujících příkladech nahraďte názvy parametrů vlastními hodnotami. 

## <a name="determine-boot-issues"></a>Určení problémů se spuštěním
Můžete zobrazit snímek obrazovky s virtuálním počítačem v Azure, který vám pomůže vyřešit problémy se spuštěním. Tento snímek obrazovky může pomoci určit, proč se nespustí virtuální hod. Následující příklad získá snímek obrazovky z `myVM` virtuálního aplikace `myResourceGroup`Windows s názvem ve skupině prostředků s názvem :

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Zkontrolujte snímek obrazovky a zjistěte, proč se virtuální mu nedaří spustit. Poznamenejte si všechny konkrétní chybové zprávy nebo chybové kódy.

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Následující příklad zastaví virtuální `myVM` hod pojmenovaný `myResourceGroup`ze skupiny prostředků s názvem :

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Počkejte, dokud virtuální hod dokončí odstranění před zpracováním k dalšímu kroku.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Vytvoření snímku z disku operačního systému virtuálního počítače

Následující příklad vytvoří snímek `mySnapshot` s názvem z disku operačního systému virtuálního počítače s názvem myVM. 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

Snímek je úplná kopie virtuálního pevného disku jen pro čtení. Nelze jej připojit k virtuálnímu virtuálnímu ms. V dalším kroku vytvoříme disk z tohoto snímku.

## <a name="create-a-disk-from-the-snapshot"></a>Vytvoření disku ze snímku

Tento skript vytvoří spravovaný disk s názvem `newOSDisk` ze snímku s názvem `mysnapshot`.  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Nyní máte kopii původního disku operačního systému. Tento disk můžete připojit k jinému virtuálnímu počítače se systémem Windows pro účely řešení potíží.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Připojení disku k jinému virtuálnímu virtuálnímu počítače se systémem Windows pro řešení potíží

Teď připojíme kopii původního disku operačního systému k virtuálnímu počítače jako datový disk. Tento proces umožňuje opravit chyby konfigurace nebo zkontrolovat další soubory protokolu aplikace nebo systému na disku. Následující příklad připojí disk `newOSDisk` s názvem virtuálního počítače s názvem `RecoveryVM`.

> [!NOTE]
> Chcete-li připojit disk, kopie původního disku operačního systému a virtuální ho virtuálního počítače pro obnovení musí být ve stejném umístění.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Připojení k virtuálnímu počítači pro obnovení a oprava problémů na připojeném disku

1. RDP k virtuálnímu počítači pro obnovení pomocí příslušných pověření. Následující příklad stáhne soubor připojení RDP pro `RecoveryVM` virtuální ho `myResourceGroup`s lokator `C:\Users\ops\Documents`s názvem a stáhne jej do "

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Datový disk by měl být automaticky rozpoznán a připojen. Zobrazení seznamu připojených svazků k určení písmene jednotky postupujte takto:

    ```powershell
    Get-Disk
    ```

    Následující ukázkový výstup ukazuje disk připojený k disku **2**. (Můžete také `Get-Volume` zobrazit písmeno jednotky):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Po namontování kopie původního disku operačního systému můžete podle potřeby provést všechny kroky údržby a řešení potíží. Jakmile vyřešíte problémy, pokračujte následujícími kroky.

## <a name="unmount-and-detach-original-os-disk"></a>Odpojení a odpojení původního disku operačního systému
Jakmile jsou chyby vyřešeny, odpojíte a odpojíte existující disk od virtuálního počítače pro obnovení. Disk nelze použít s žádným jiným virtuálním počítačem, dokud není uvolněna zapůjčení připojující disk k virtuálnímu počítači pro obnovení.

1. Z relace RDP odpojte datový disk na virtuálním počítači pro obnovení. Potřebujete číslo disku z `Get-Disk` předchozí rutiny. Potom použijte `Set-Disk` k nastavení disku jako offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Potvrďte, že disk je `Get-Disk` nyní nastaven jako offline pomocí znovu. Následující ukázkový výstup ukazuje, že disk je nyní nastaven jako offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Ukončete relaci RDP. Z relace Azure PowerShell, odeberte disk s názvem `newOSDisk` z virtuálního počítače s názvem RecoveryVM.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Změna disku operačního systému pro ohrožený virtuální modul

Azure PowerShell můžete použít k výměně disků operačního systému. Není nutné odstranit a znovu vytvořit virtuální ho.

Tento příklad zastaví název `myVM` virtuálního počítače `newOSDisk` a přiřadí disk pojmenovaný jako nový disk operačního systému. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Ověření a povolení diagnostiky spouštění

Následující příklad umožňuje diagnostické rozšíření na `myVMDeployed` virtuálním počítači `myResourceGroup`pojmenované ve skupině prostředků s názvem :

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k virtuálnímu počítači, [přečtěte si článek Poradce při potížích s připojením RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači najdete [v tématu Řešení problémů s připojením aplikací na virtuálním počítači se systémem Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Další informace o používání Správce prostředků najdete v [tématu Přehled Správce prostředků Azure](../../azure-resource-manager/management/overview.md).
