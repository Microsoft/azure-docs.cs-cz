---
title: Použití virtuálního počítače pro řešení potíží s Windows s Azure PowerShell | Microsoft Docs
description: Naučte se řešit problémy s virtuálními počítači s Windows v Azure připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure PowerShell
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
ms.openlocfilehash: 63d5e4bcc57a734fc5ea455da7c2db940a4b8ec3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654680"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Řešení potíží s virtuálním počítačem s Windows připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure PowerShell
Pokud váš virtuální počítač s Windows v Azure najde chybu spuštění nebo disku, možná budete muset provést kroky pro řešení potíží na samotném disku. Běžným příkladem může být neúspěšná aktualizace aplikace, která brání úspěšnému spuštění virtuálního počítače. Tento článek podrobně popisuje, jak pomocí Azure PowerShell připojit disk k jinému virtuálnímu počítači s Windows a opravit případné chyby a pak opravit původní virtuální počítač. 

> [!Important]
> Skripty v tomto článku se vztahují pouze na virtuální počítače, které používají [spravovaný disk](../managed-disks-overview.md). 

 

## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Nyní můžeme pomocí Azure PowerShell změnit disk s operačním systémem pro virtuální počítač. Už nepotřebujeme virtuální počítač odstranit a znovu vytvořit.

Proces řešení potíží je následující:

1. Zastavte ovlivněný virtuální počítač.
2. Vytvořte snímek z disku s operačním systémem virtuálního počítače.
3. Vytvořte disk ze snímku disku s operačním systémem.
4. Připojte disk jako datový disk k virtuálnímu počítači pro obnovení.
5. Připojte se k virtuálnímu počítači pro obnovení. Upravte soubory nebo spusťte libovolné nástroje, abyste opravili problémy s kopírovaným diskem s operačním systémem.
6. Odpojte a odpojte disk z virtuálního počítače pro obnovení.
7. Změňte disk s operačním systémem pro ovlivněný virtuální počítač.

Pomocí příkazů pro opravu virtuálního počítače můžete automatizovat kroky 1, 2, 3, 4, 6 a 7. Další dokumentaci a pokyny najdete v tématu [Oprava virtuálního počítače s Windows pomocí příkazů pro opravu virtuálního počítače Azure](repair-windows-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ujistěte se, že máte nainstalovanou [nejnovější Azure PowerShell](/powershell/azure/) a že jste se přihlásili ke svému předplatnému:

```powershell
Connect-AzAccount
```

V následujících příkladech nahraďte názvy parametrů vlastními hodnotami. 

## <a name="determine-boot-issues"></a>Určení problémů se spouštěním
Můžete si Zobrazit snímek obrazovky virtuálního počítače v Azure, který vám umožní řešit problémy se spouštěním. Tento snímek obrazovky vám může přispět k identifikaci příčin, proč se virtuální počítač nepodaří spustit. Následující příklad získá snímek obrazovky z virtuálního počítače s Windows `myVM` ve skupině prostředků s názvem `myResourceGroup` :

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Zkontrolujte snímek obrazovky, abyste zjistili, proč se virtuální počítač nedaří spustit. Poznamenejte si všechny konkrétní chybové zprávy nebo kódy chyb.

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Následující příklad zastaví virtuální počítač pojmenovaný `myVM` ze skupiny prostředků s názvem `myResourceGroup` :

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Počkejte, než se virtuální počítač dokončí a teprve potom proveďte zpracování na další krok.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Vytvoření snímku z disku s operačním systémem virtuálního počítače

Následující příklad vytvoří snímek s názvem `mySnapshot` z disku s operačním systémem virtuálního počítače s názvem ' myVM '. 

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

Snímek je plná kopie VHD, která je jen pro čtení. Nedá se připojit k virtuálnímu počítači. V dalším kroku vytvoříme z tohoto snímku disk.

## <a name="create-a-disk-from-the-snapshot"></a>Vytvoření disku ze snímku

Tento skript vytvoří spravovaný disk s názvem `newOSDisk` z snímku s názvem `mysnapshot` .  

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
Teď máte kopii původního disku s operačním systémem. Tento disk můžete připojit k jinému virtuálnímu počítači s Windows pro účely řešení potíží.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Připojte disk k jinému virtuálnímu počítači s Windows pro řešení potíží.

Nyní připojíme kopii původního disku s operačním systémem k virtuálnímu počítači jako datový disk. Tento proces umožňuje opravit chyby v konfiguraci nebo zkontrolovat soubory protokolů aplikace nebo systému na disku. Následující příklad připojí disk s názvem `newOSDisk` k virtuálnímu počítači s názvem `RecoveryVM` .

> [!NOTE]
> Aby bylo možné připojit disk, kopie původního disku s operačním systémem a virtuálního počítače pro obnovení musí být ve stejném umístění.

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

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Připojte se k virtuálnímu počítači pro obnovení a opravte problémy s připojeným diskem.

1. RDP na virtuální počítač pro obnovení pomocí příslušných přihlašovacích údajů. Následující příklad stáhne soubor připojení RDP pro virtuální počítač `RecoveryVM` ve skupině prostředků s názvem `myResourceGroup` a stáhne ho do: `C:\Users\ops\Documents` "

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Datový disk by měl být automaticky rozpoznán a připojen. Zobrazte seznam připojených svazků a určete písmeno jednotky následujícím způsobem:

    ```powershell
    Get-Disk
    ```

    Následující příklad výstupu ukazuje disk připojený k disku **2**. (Můžete také použít `Get-Volume` k zobrazení písmene jednotky):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Po připojení kopie původního disku s operačním systémem můžete podle potřeby provádět libovolné kroky údržby a řešení potíží. Jakmile vyřešíte problémy, pokračujte následujícími kroky.

## <a name="unmount-and-detach-original-os-disk"></a>Odpojte a odpojte původní disk s operačním systémem
Po vyřešení chyb odpojte a odpojte stávající disk od virtuálního počítače pro obnovení. Disk nemůžete použít s žádným jiným virtuálním počítačem, dokud se neuvolní zapůjčení disku k virtuálnímu počítači pro obnovení.

1. V rámci relace RDP odpojte datový disk na VIRTUÁLNÍm počítači pro obnovení. Z předchozí rutiny budete potřebovat číslo disku `Get-Disk` . Pak použijte `Set-Disk` k nastavení disku jako offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Potvrďte, že je disk teď nastavený jako offline, a `Get-Disk` to pomocí znovu. Následující příklad výstupu ukazuje, že disk je nyní nastaven jako offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Ukončete relaci protokolu RDP. Z relace Azure PowerShell vyjměte disk s názvem `newOSDisk` z virtuálního počítače s názvem "RecoveryVM".

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Změna disku operačního systému pro ovlivněný virtuální počítač

K prohození disků s operačním systémem můžete použít Azure PowerShell. Nemusíte odstranit a znovu vytvořit virtuální počítač.

V tomto příkladu se zastaví virtuální počítač s názvem `myVM` a přiřadí se disk s názvem `newOSDisk` jako nový disk s operačním systémem. 

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

Následující příklad aktivuje diagnostické rozšíření na virtuálním počítači s názvem `myVMDeployed` ve skupině prostředků s názvem `myResourceGroup` :

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači, přečtěte si téma [řešení potíží s připojením RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Problémy s přístupem k aplikacím běžícím na vašem VIRTUÁLNÍm počítači najdete v tématu [řešení potíží s připojením aplikací na virtuálním počítači s Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Další informace o použití Správce prostředků naleznete v tématu [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md).
